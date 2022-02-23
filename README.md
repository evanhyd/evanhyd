# Things I need to pay attention next time:
  
use huge buffer to speed up reading if needed  
```c++
#define INPUT_SIZE (250<<10)
int _i0=0;
char _,_i[INPUT_SIZE+5];
#define su(x) do{for(x=_i[_i0++]-48;47<(_=_i[_i0++]);x=x*10+_-48);}while(0)
```
  
  
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
Bar Foo(const Bar& rhs)
{
    return this->Interact(rhs); //if Interact function returns Bar&, it may evoke copy constructor instead of move constructor
}

```


allocate new resources before deleting the old one  
```c++
delete old_data; //make sure to check self assignment (try RAII with copy and swap)  
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
#pragma GCC optimize ("O2")
#pragma GCC optimize ("O3")
#pragma GCC optimize ("Ofast")
#pragma GCC target ("avx2")
```

# guides I follow
[C++ Google Style Guide](https://google.github.io/styleguide/cppguide.html)  
[C++ operator overloading guides](https://stackoverflow.com/questions/4421706/what-are-the-basic-rules-and-idioms-for-operator-overloading)   
[C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)  


