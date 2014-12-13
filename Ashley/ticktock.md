This problem was a cool one in that it could be solved very simply using some basic number theory. 

First, we look at the given source code. 

In `tick-tock.py` there are two important parts. First, let's look at the first half of the problem. 
```python
secretz = [(1, 2), (2, 3), (8, 13), (4, 29), (130, 191), (343, 397), (652, 691), (858, 1009),
           (689, 2039), (1184, 4099), (2027, 7001), (5119, 10009), (15165, 19997), (15340, 30013),
           (29303, 70009), (42873, 160009), (158045, 200009)]

print '\033[2J'

for (r,m) in secretz:
  if count(num,m,"%d %% %d"%(num,m)) != r:
    print
    print "%d %% %d != %d... WRONG"%(num,m,r)
    sys.exit(0)
  else:
    print
    print "%d %% %d == %d... GOOD"%(num,m,r)
    time.sleep(2)
    print '\033[2A'
    print " "*90
```

The code is relatively straightforward, and we can easily tell that the for-loop checks that the first number we input is a mod b for every (a,b) in secretz. We know how to deal with this. Chinese Remainder Theorem (CRT) is our friend!

Since while simple, CRT requires quite a bit of computational power, let's write some python.
```python
def mul_inv(a, b):
    b0 = b
    x0, x1 = 0, 1
    if b == 1: return 1
    while a > 1:
        q = a / b
        a, b = b, a%b
        x0, x1 = x1 - q * x0, x0
    if x1 < 0: x1 += b0
    return x1
 
def chinese_remainder(n, a, lena):
    p = i = prod = 1; sm = 0
    for i in range(lena): prod *= n[i]
    for i in range(lena):
        p = prod / n[i]
        sm += a[i] * mul_inv(p, n[i]) * p
    return sm % prod
 
if __name__ == '__main__':
    n = [2, 3, 13, 29, 191, 397, 691, 1009, 2039, 4099, 7001, 10009, 19997, 30013, 70009, 160009, 200009]
    a = [1, 2, 8, 4, 130, 343, 652, 858, 689, 1184, 2027, 5119, 15165, 15340, 29303, 42873, 158045]
    print(chinese_remainder(n, a, len(n)))
```
Upon execution, the code returns 83359654581036155008716649031639683153293510843035531. This gives us the first argument for our input!

However, it's a bit too early to be happy. The second part still remains. 
```python
if powmod(num,num2,200009*160009,"%d ^ %d %% %d"%(num,num2,200009*160009)) != 1:
  print
  print "%d ^ %d %% %d != 1... WRONG"%(num,num2,200009*160009)
  sys.exit(0)
else:
  print "Congratulations! The flag is: %s_%s"%(sys.argv[1],str(num2))
```
Thankfully, this part is even more straightforward than the first. We just need to find an x such that 83359654581036155008716649031639683153293510843035531^x is congruent to 1 mod 200009*160009. 

Euler's theorem states that a^&phi;(n) = 1 mod n for any value of a. We can take advantage of this.
We can use a single WolframAlpha query to calculate &phi;(200009*160009), and find that our second argument should be 32002880064. Upon execution of the program, we successfully find our flag:
`83359654581036155008716649031639683153293510843035531_32002880064`