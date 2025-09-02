---
title: 'HackINI-2k25 Cryptography Challenges Writeups'
tags: ["CTF","Cryptography", "writeups", "Cyber Security","HackInI","Shellmates"]
categories: ["CTF Writeups","Cryptography"]
math: true
image:
  path: https://i.postimg.cc/j5VFfX2C/hackini-2025-log.jpg
---


----------
### **Introduction**
I will be covering here details writeups to cryptography challenges I wrote in the HackINI 2025 which is a CTF organised by Shellmates club each year mainly for new CTF players. The challenges are: 
- RSA Leaks
- ECB Oracle

----------
### **RSA Leaks**
#### Overview
- Difficulty: Easy
- Provided files: [challenge.py](https://github.com/Shellmates/HackINI-2K25-CTF-Public/blob/main/crypto/rsa_leaks/challenge/challenge.py)
- Description:

> **They claimed that as long as the output appears random, it is secure**
{: .prompt-info }

#### Initial Analysis
Let's start by reading the source code and understanding what each part does:
```python
def lcg(x, a, c, m):
    return (a * x + c) % m
```
It takes a seed $X$ and generates another number using a linear congruential generator (LCG). 

> An LCG is a simple pseudorandom number generator that follows the formula $X_{n+1} = (aX_n + c) \bmod m$, where $a$ is the multiplier, $c$ is the increment, and $m$ is the modulus. While LCGs are fast and simple, they are cryptographically weak because their output is predictable: given just a few consecutive outputs, an attacker can determine the internal parameters and predict all future values, you can find out more about how are LCG weak here: [LCG weakness demonstrated](https://msm.lt/posts/cracking-rngs-lcgs/)
{: .prompt-tip }

Then we have:
```python
p , q = getPrime(512) , getPrime(512)
while p == q :
    q = getPrime(512)
r = getPrime(2048)
while r <= p and r <= q :
    r = getPrime(2048)
```
This simply generates 3 prime numbers: $p$, $q$ (512 bits) and $r$ (2048 bits).

Next we have:
```python
x0 = random.getrandbits(1800)
x = []
for i in range(12) :
    if i in (0,2,4,6,8,10):
        x.append(x0)
    x0 = lcg(x0,p,q,r)
```
What this does is generate an LCG sequence of 12 numbers and only return those with an even index with: $p$ being **multiplier**, $q$ being **increment** and $r$ being **modulus**.

Lastly we have:
```python
n = p*q*r
e = 65537
m = bytes_to_long(FLAG.encode())
c = pow(m,e,n)

print(f"n = {n}")
print(f"e = {e}")
print(f"c = {c}")
print(f"x = {x}")
```
This will create an RSA cryptosystem with 3 primes, encrypt the flag with it and print the RSA public key, the encrypted flag $C$ and the generated LCG sequence.

#### Solution Steps
In order to get the flag it is is required to have the private key d, which requires recovering at least 2 of the prime factors used in the encryption. The only way to do that is by breaking the LCG sequence since its parameters are the prime factors used in RSA. It is possible to break the LCG with 6 samples, but the problem here is that the samples are not consecutive, however there is a way to actually recover the parameters.

We begin from the full LCG:

$$
x_{n+1} \equiv p\,x_n + q \pmod r
$$

Expanding two steps gives:

$$
\begin{aligned}
x_{n+2} &\equiv p\,x_{n+1} + q \pmod r \\
        &\equiv p\,(p x_n + q) + q \pmod r \\
        &\equiv p^2 x_n + (p+1)q \pmod r
\end{aligned}
$$

If we observe every second state, i.e. the subsequence $$(X_i = x_{2i})$$ we gave to participants, then
this subsequence itself satisfies an LCG:

$$
\begin{aligned}
X_{i+1} &\equiv A\,X_i + C \pmod r
\end{aligned}
$$

with the parameters

$$
\begin{aligned}
A &\equiv p^2 \pmod r, \\
C &\equiv (p+1)q \pmod r.
\end{aligned}
$$

(When ($$p^2 < r$$) and ($$(p+1)q < r$$) due to $$r$$ being way larger than $$p$$ and $$q$$ as integers there is no wrap-around and the congruences are equalities:($$A = p^2$$) and $$(C = (p+1)q)$$).

Now we can break the subsequence 

$$
X_i = x_{2i},
$$ 

recover the parameters $$A$$, $$C$$, and $$r$$. From these values we obtain:

$$
\begin{aligned}
p &= \sqrt{A}, \\
q &= \frac{n}{r \cdot p}.
\end{aligned}
$$

We will use the [article](https://msm.lt/posts/cracking-rngs-lcgs/) mentionned above to break the LCG, now all we have left is the attack implementation.

#### Python Solver
```python
from math import gcd
from functools import reduce
from gmpy2 import iroot
from Crypto.Util.number import long_to_bytes

def egcd(a, b):
    x0, x1, y0, y1 = 1, 0, 0, 1
    while b != 0:
        q, a, b = a // b, b, a % b
        x0, x1 = x1, x0 - q * x1
        y0, y1 = y1, y0 - q * y1
    return a, x0, y0


def modinv(b, n):
    b = b % n
    g, x, _ = egcd(b, n)
    if g != 1:
        raise ValueError(f"No modular inverse for {b} mod {n}")
    return x % n


def crack_unknown_increment(states, modulus, multiplier):
    increment = (states[1] - states[0]*multiplier) % modulus
    return modulus, multiplier, increment

def crack_unknown_multiplier(states, modulus):
    multiplier = ((states[2] - states[1]) % modulus) * modinv((states[1] - states[0]) % modulus, modulus) % modulus
    return crack_unknown_increment(states, modulus, multiplier)

def crack_unknown_modulus(states):
    diffs = [s1 - s0 for s0, s1 in zip(states, states[1:])]
    zeroes = [t2*t0 - t1*t1 for t0, t1, t2 in zip(diffs, diffs[1:], diffs[2:])]
    modulus = abs(reduce(gcd, zeroes))
    return crack_unknown_multiplier(states, modulus)

def recover_p_q(A, C, r, n):
    p, is_square = iroot(A, 2)
    if not is_square:
        raise ValueError("A is not a perfect square")
    q = n // (r * p)
    return int(p), int(q)

def decrypt_rsa(n, e, c, p, q, r):
    phi = (p - 1) * (q - 1) * (r - 1)
    d = modinv(e, phi)
    if d is None:
        raise ValueError("e not invertible mod phi")
    m = pow(c, d, n)
    return long_to_bytes(m).decode()

# given values
n = 4753655573275027817361730548247680584762305501743841141034071595007602068627308316137483570048679355146725929170164783272405126440511470571913733547300136635916930582196022662377770388351470099482257089632564779939781564156712187404875070500519511930818051365890374587971446316289247022240734129004982089508747802667700362005123032332380127149960178245198342979274528775487672302442474577456018305959069467128134307024498680353159380825432886627087932741123471198234122890373426534798095186011044484790581433125337226781795190015387018890180464735772817835270054975519441010434647671694425996252164456408429052675278559648010364714528228549783429493163341925196583739658365842079034270623140734542371566181626401383634179182271239059029281913715100588441701170829112908620107672327410706078753852811071588303919045010343376844499751286315331033017734877554462764208235099153264865296697351625348356355594098874136553773519101
e = 65537
c = 2263446943915905547093717198907827103957469607438834093426435407116951948981745264008126334863336238271227419220588837270126736364032912709113298458300415129535008110750037707524064394643495885028707856160777171094306734481381990172614960978669951590364746135956300432548734913206828507084697393442016511042367004491035088128688808208279254497025700237687773655038021411874292125990160928073171316626820561899177707342268725630971262843797269330053046809900737466939393360471929170182520482222107219780265993645364184998601172290449575944838096533668416943038264743172449230268770272296815981364151097026898240063600989102043949202769376329237305407050248593229341927815898959365385346330707232902403016905208595369999643283831003035100808874232652113980753651708681162310108180230322756886286082584067594248220166137520173537063333494028491821268115197335901967158015985132051436962416250597915263292843037480514874780346654
X = [54732470613203053649171214360090912954819040695912827547558213398233266309167498372350633924138290744249369255812485310444404668547489409926952002583927803538138111890424670729483447228435674780449431756201939416429434431178133642185923288958280379347990537553390515874435848745748484704182519124581305289671554772609676018229183137578381388101785141064234261507042245350240585939175510019942707499027248683814309753854400468790122958481530159969012734193761291510431705447676548499691053992529772346041586798635530423570185277459566075774837, 21434352748600817565799152223080108571396898831315680286837876407192998106744522563149543808493566518491550390814232017291569457382144529597319719245088673308555459302667642315904304492875833283130834527206226795378370828788333107918503335121913654560501393563118721844086888855648660519338945371422294529923183674800542479558023804147985494541803774387936724846748405257689139506352405949948864176247640134364763122243150003921885017301537438991110519003165748252491106141797902586140582026983307991561578967803024932689603441064634272004878767017111499916163135870534592586966141173713341288136356114547575728456183, 7212455263908971140383331002924197837097891032468096336449565710404155182332129510500295027634961921308267853697684054389944723420357948278048835582852782511475145581152125026850589010630275570735679960420365328128789224284567877155497032650060035652948864421852088792944037466081291387983831488683603474873342929049181860411105004260806538255324143216852430677994984893396967327605122878364165973507097889032955336016675426070416749840474160599470083810025248597366520610763052798652593463077009834901998484601299646697595978914518439644858610249973301846523085866811002809080197132131346625176160918049430714674301, 11880145763624377780579216000604356739863522783649553633132571643558595128492454899494015735325590673308144569515214323467347558476452121068960351648117201807549650502799646811207811221757000867019546197165517662889871185949961137349239051723178424064651763946355998330373901538338256096203041807828394207294031716695078266434436937996478111153123264485650326314992996665274127377902065872296094013251454036163263906093238523525344671608337997203831005868715271738721613273302934118569152086463339831504437999204327821055213853876778641737164271121210259074411869775131818048697029101705548080438533464903876700235026, 2653470042756455059224352054950277696973878493694573205434727052193793803363778194140183996763894918239727382000949465100136403536546771399662939344810137147397511424415104863005811252770557161391837343258636194892951660006743035592366925364830124641299992443153741754414109098520197476251114950932714034600111309592746705366587476691171702702930740295869182554497187429815707619328475789414223628232857348293836105699816068090185476315303252648329217313747990234999971862161259219099837972377199269111627515124886922825122248745831091337187942587347011901601609273782682238865706203300099537092858169230385505126918, 18849050417534146151225725186969944565522976481165775711029044388130731275304592745501422960426816115442503814662322814202528434689884867796885141856621953041047535039704178411364358300385146164574029581890606819697139219119292426828012400517781568175150063480813964600947228563470623177367010283812894268244123558442905997616742232205638163193231877821297051953662047491944723884864664116895623281965558400315672001890259956112430316054390862210539702230587400262878950743613110927206110021535108555013929537050775702014143980773560654456532133295793342236778562569074768190817177881077958449539043421633672673307848]

# break the subsequence
r, A, C = crack_unknown_modulus(X)

# recover p and q
p, q = recover_p_q(A, C, r, n)

# decrypt flag
flag = decrypt_rsa(n, e, c, p, q, r)
print(flag)
```
There goes the flag: **shellmates{N0_w4y_tH3Y_g3n3r4tEd_r4Nd0m_NUMBERs_Th1S_w4Y_iN_tHe_p4$T}**

---
### **ECB Oracle**
#### Overview
- **Difficulty**: Easy
- **Provided files**: [server.py](https://github.com/Shellmates/HackINI-2K25-CTF-Public/blob/main/crypto/ecb_oracle/challenge/server.py)
- **Description**:

> **A good cryptographer is always good at math and has good logic. Can you prove that to me?**
{: .prompt-info }

#### Initial Analysis

> Looking at the challenge name, we can clearly conclude it is an AES ECB oracle. We all know the problem with AES ECB: it is the fact that encrypting the same block twice using the same key gives us the same ciphertext. AES ECB (Electronic Codebook) mode encrypts each 16-byte block independently, creating a vulnerability where identical plaintext blocks produce identical ciphertext blocks.
{: .prompt-tip }

Let's start by reading the source code and understanding what each part does:

```python
def generate_seperator(x):
    n = len(x)
    M = (n+1) if (n>0) else 1
    l = sum( ord(x[i]) * (i+1) for i in range(n) )
    s = sum( ord(x[i])**2 for i in range(n) )
    return (l + s + n) % M
```

This function calculates a "separator" position based on the input string $x$. It uses a mathematical formula involving:
- $l$: A weighted sum where each character's ASCII value is multiplied by its position (1-indexed)
- $s$: Sum of squares of all ASCII values  
- The final separator is $(l + s + n) \bmod M$ where $M = n+1$ and $n$ is the string length

Then we have:
```python
def transform_input(x, secret = FLAG):
    seperator = generate_seperator(x)
    return x[:seperator] + secret + x[seperator:]
```

This is the key function! It takes our input $x$, calculates a separator position, and inserts the secret FLAG at that position. So if separator = 5, it becomes: $x[0:5] + \text{FLAG} + x[5:]$

Next we have:
```python
def encrypt(pt):
    cipher = AES.new(KEY, AES.MODE_ECB)
    return cipher.encrypt(pad(pt.encode(), BLOCK_SIZE))

def oracle(x):
    pt = transform_input(x, FLAG)
    return encrypt(pt).hex()
```

The transformed plaintext is encrypted using AES in ECB mode with PKCS7 padding. This creates an encryption oracle that we can query with different inputs.

#### Solution Steps

In order to get the flag, we need to exploit the ECB mode vulnerability combined with our ability to control the separator function. The attack strategy is to recover the flag byte by byte using a controlled separator positioning technique.

We know that the flag begins with `shellmates{`; to discover the next byte of the flag, we need to craft an input that results in the following structure being encrypted where the next unknown byte is aligned at a block boundary.

However, this process is complicated by the fact that we cannot simply provide an input of arbitrary length. The input is modified by the separator function, so our goal is to craft an input such that the computed separator value precisely aligns the next unknown byte of the flag as the final byte of an AES block.

To accomplish this, we must reverse the `generate_seperator(x)` function and produce an input string that yields the desired separator value.

##### Reversing the `generate_seperator(x)` Function

Given the separator function, we need to find an input string $x$ that produces a specific separator value $k$.

##### Mathematical Formulation

The separator function can be expressed as:

$$L = \sum_{i=0}^{n-1} (i + 1) \cdot x_i \quad \text{(Weighted linear sum)}$$

$$S = \sum_{i=0}^{n-1} x_i^2 \quad \text{(Quadratic sum)}$$

$$\text{separator} = (L + S + n) \bmod (n + 1)$$

##### Inversion Problem

Given desired separator value $k$, solve for $x$ in:
$$(L + S + n) \equiv k \pmod{n + 1}$$

##### Solution Strategy

**Step 1: Set the target string length**  
Set the target string length to $n = k$. This choice simplifies the modulo $(n + 1)$ arithmetic space.

**Step 2: Fix Prefix Characters**  
Fix the first $n - 1$ characters of the input to a constant character $\alpha$, making $L$ and $S$ predictable:

$$L_{\text{prefix}} = \alpha \cdot \sum_{i=0}^{n-2} (i + 1) = \alpha \cdot \frac{(n-1) \cdot n}{2}$$

$$S_{\text{prefix}} = (n - 1) \cdot \alpha^2$$

**Step 3: Solve for the Last Character**  
Given $L_{\text{prefix}}$ and $S_{\text{prefix}}$, the contribution of the last character $y$ must satisfy:

$$L_{\text{prefix}} + S_{\text{prefix}} + y \cdot n + y^2 + n \equiv k \pmod{n + 1}$$

**Step 4: Iterate Over Possible Fixed Characters**  
Iterate over possible values of $\alpha$ to maximize the chance of finding a valid $y$.

**Step 5: Check for Printable Character**  
Once $y \bmod (n + 1)$ is determined, verify whether there exists a printable ASCII character satisfying this condition.

##### ECB Exploitation Algorithm

1. We start with the known prefix of the flag: shellmates{.

2. For each subsequent unknown byte of the flag at index $i$, perform the following steps:
   - Compute the required separator value: $k = 15 - (i \bmod 16)$
   - Generate a prefix string such that the separator equals $k$, using the reversed `generate_string_for_seperator` function
   - Send the input to the server
   - Brute-force all possible printable characters for the next byte of the flag:
     - For each candidate character, append it to the known flag and encrypt the corresponding input. Make sure the input has a separator value that is safe and won't separate the guessed flag from the prefix we made
     - Compare the resulting ciphertext block against the reference block to identify the correct byte
   - Once we find the character }, we exit since we found the flag

#### Python Solver

```python
from pwn import *
import string

context.log_level = 'error'
HOST = "localhost"
PORT = 1337
conn = remote(HOST, PORT)

PRINTABLE_CHARS = [char for char in string.printable if char not in string.whitespace]

def get_seperator_block_values(recovered_flag):
    flag_length = len(recovered_flag)
    block_number = flag_length // 16
    offset_in_block = flag_length % 16
    seperator = 16 - ( offset_in_block + 1 )
    if seperator == 0 :
        seperator += 16 * (block_number + 1)
    return seperator , block_number

def generate_string_for_seperator(separator):
    if separator < 0:
        raise ValueError("separator must be >= 0")
    if separator == 0:
        return ""
    n = separator
    M = n + 1
    for fixed_char in string.ascii_uppercase:
        alpha = ord(fixed_char)
        L_prefix = alpha * ((n - 1) * n // 2)
        Q_prefix = (n - 1) * (alpha * alpha)
        P = L_prefix + Q_prefix
        y_mod = None
        for cand in range(M):
            if (cand * cand + n * cand + P) % M == 0:
                y_mod = cand
                break
        if y_mod is None:
            continue
        y = None
        for printable in range(33, 127):
            if printable % M == y_mod:
                y = printable
                break
        if y is None:
            cand = y_mod
            while cand < 32:
                cand += M
            if cand <= 126:
                y = cand
            else:
                cand = y_mod
                while cand > 126:
                    cand -= M
                if 32 <= cand <= 126:
                    y = cand
                else:
                    continue
        prefix = fixed_char * (n - 1)
        last_char = chr(y)
        return prefix + last_char
    raise RuntimeError("No solution found for any fixed prefix character")

def generate_input(recovered_flag):
    s, block_number = get_seperator_block_values(recovered_flag)
    return generate_string_for_seperator(s), block_number, s

def encrypt_input(conn, input_str):
    conn.recvuntil(b"[+] Enter a message here: ")
    conn.sendline(input_str.encode())
    line = conn.recvuntil(b"[+] Encrypted message: ").decode()
    line += conn.recvline().decode()
    return line.strip().split(": ",1)[1]

def generate_safe_fake_input(x, fake_flag_guess):
    attempt = x + fake_flag_guess
    t = len(attempt)
    while generate_seperator(attempt) < t:
        attempt += "A"
    return attempt

def get_char_block(crafted_input, recovered_flag):
    return (len(crafted_input) + len(recovered_flag)) // 16

def main(conn):
    recovered_flag = "shellmates{"
    while True:
        crafted_input, block_number, sep = generate_input(recovered_flag)
        ct = encrypt_input(conn, crafted_input)
        blocks = [ct[i:i+32] for i in range(0, len(ct), 32)]
        for char in PRINTABLE_CHARS:
            fake_flag_guess = recovered_flag + char
            fake_input = generate_safe_fake_input(crafted_input, fake_flag_guess)
            test_ct = encrypt_input(conn, fake_input)
            test_blocks = [test_ct[i:i+32] for i in range(0, len(test_ct), 32)]
            test_block_number = get_char_block(crafted_input, recovered_flag)
            if blocks[test_block_number] == test_blocks[test_block_number]:
                recovered_flag += char
                if char == '}':
                    print(f"Flag is found: \033[1;32m{recovered_flag}\033[0m")
                    return
                break
        else:
            print("No matching char found this round")
            return

main(conn)
```

There goes the flag: **shellmates{EcB_w1ll_alW4Y$_b3_THE_w34KEsT_aEs_MOd3}**

--- 
### **References**
- [LCG weakness demonstrated](https://msm.lt/posts/cracking-rngs-lcgs/)

![Primeagen](https://media1.tenor.com/m/hYU0XdvEzmAAAAAC/theprimeagen-primeagen.gif)

That's all for this blog, thank you for reading.
