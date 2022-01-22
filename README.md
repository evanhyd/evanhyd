# UnboxTheCat

# Things I need to pay attention next time:
  
use huge buffer to speed up reading if needed  
  
  
  
do not mix up signed and unsigned in arithmetic calculation
```c++
constexpr int kCapacity = 10;
std::string a, b;
std::cin >> a >> b;
if((kCapacity - a.size()) > b.size()) //oh no, kCapacity - a.size() can underflow
{
    //do something
}
```
  
  
  
'\n' over std::endl, except for interactive console  
```c++
std::cout << "hello" << std::endl; //flushing may be unnecessary
```
  
  
  
pointer/reference to priority queue's top can be invalidated!  
```c++
priority_queue que;
while(!que.empty())
{
    T& ref = que.top();
    que.push(ref.Search()); //ref may be invalidated
}
```


return reference directly may not evoke move semantics 
```c++
Bar operand+(const Bar& addend)
{
    Bar sum(*this);
    return sum += addend; //if += operator returns Bar&, it may evoke copy constructor instead of move constructor
}

```


deleting old resources before allocating new resources can be dangerous
```c++
delete old_data; //make sure to check self assignment (try copy and swap strategy)
Bar *new_data = new Bar(); //if exception happens, then it fails to allocate new resources AND the old data is lost!
old_data = new_data;
```


# some variable names
```
srce     (source)
dest     (destination)
first, last   (denote range [a, b) )
begin,  end     (denote range [a, b)  )
i, j, k         (index for nested loops)
temp   (swapping variable)
prev, curr, next(nxt) (holding objects/variables)
arr  (acronym of array)
vec  (vector)
lst, ls   (list)
mp   (map, hashmap)
addr   (address)
ptr  (pointer)
fn  (function)
```


# tricks may or may not work in lxxxcode
```c++
#pragma GCC optimize ("Ofast")
#pragma GCC target ("avx2")
```
