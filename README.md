# Things I need to pay attention next time:
# Hacky method to speed up reading unsigned integer (stolen from DMOJ)   
## Huge buffer  
```c++
#define INPUT_SIZE (250<<10)
int _i0=0;
char _,_i[INPUT_SIZE+5];
#define su(x) do{for(x=_i[_i0++]-48;47<(_=_i[_i0++]);x=x*10+_-48);}while(0)
```
  
## Hacky unsigned integer reader  
```c++
#define scan(x) do{while((x=getchar())<'0'); for(x-='0'; '0'<=(_=getchar()); x=(x<<3)+(x<<1)+_-'0');}while(0)
char _;
```
  
  
  
# Do not mix up signed/unsigned variables
## Bad
```c++
constexpr int kCapacity = 10;
std::string a, b;
std::cin >> a >> b;
if((kCapacity - a.size()) > b.size()) //oh no, kCapacity - a.size() can easily underflow
{
    //do something
}
```
## Better
```c++
constexpr int kCapacity = 10;
std::string a, b;
std::cin >> a >> b;
if((kCapacity - static_cast<int>(a.size())) > static_cast<int>(b.size())) // be careful under/overflow
{
    //do something
}
```
  
  
# Do not flush the output buffer for no reason with exception of coding interactive console
## Bad
```c++
for(int i = 0; i < N; ++i)
{
    std::cout << "hello" << std::endl; //flushing buffer may be unnecessary except when debugging
}
```
## Better
```c++
for(int i = 0; i < N; ++i)
{
    std::cout << "hello" << '\n'; //flushing buffer may be unnecessary except when debugging
}
std::cout << std::flush;
```
  
  
  
# Reference to priority queue's top can be invalidated!  
## Bad
```c++
std::priority_queue<T> que;
while(!que.empty())
{
    T& tp = que.top();
    que.push(T()); //tp may point toward other objects
}
```
## Better 
```c++
std::priority_queue<T> que;
while(!que.empty())
{
    T tp = que.top();
    que.push(T()); //does not affect tp
}
```
  
  
  
# Allocate new resources before deleting the old one  
## Bad  
```c++
void Allocate(const Bar& obj)
{
    //if this is self assignment, then it always lose the data
    delete old_data;
    
    //if exception throws, then it fails to allocate new resources AND the old data is lost
    Bar *new_data = new Bar(obj);
    old_data = new_data;
}
```
## Better
```c++
void Allocate(const Bar& obj)
{
    Bar *new_data = new Bar(obj);
    delete old_data;
    old_data = new_data; //make sure to check self assignment
}
```


# Exchange instead of swapping the values in move constructor
## Bad  
```c++
Bar(Bar&& rhs) : Bar() //calling default constructor
{
    swap(*this, rhs);
}
```  
## Better
```c++
Bar(Bar&& rhs) : data_(std::exchange(rhs.data_, nullptr)), size_(std::exchange(rhs.size_, 0))
{
    //rest of the code
}
```  

# Do not copy/fill/move directly on the raw memory
## Bad
```c++
void Construct(T* raw_memory, int size, T& default_value)
{
    for(int i = 0; i < size; ++i)
    {
        raw_memory[i] = default_value; //undefined behavior! calling copy assignment on uninitialized object
    }
}
```
## Slightly Better
```c++
void Construct(T* raw_memory, int size, T& default_value)
{
    for(int i = 0; i < size; ++i)
    {
        operator new(raw_memory + i) T(default_value); //placement new copy construct
    }
}
```
## Better
```c++
void Construct(T* raw_memory, int size, T& default_value)
{
    std::uninitialized_fill(raw_memory, raw_memory + i, default_value);
}
```
  
  
Rule of Three?  
Rule of Five?!  
Rule of Four and an Half!  
Rule of Zero!!!  
  
  
  
# some variable names (stop using "temp" all the time pls)  
```
srce                   (source)
dest                   (destination)
first, last            (denote range [a, b))
begin,  end            (denote range [a, b))
i, j, k                (index for nested loops)
prev, curr, next(nxt)  (variables)
arr                    (array)
vec                    (vector)
que                    (queue/deque)
lst, ls                (list)
mp                     (map/hashmap)
addr                   (address)
ptr                    (pointer)
fn                     (function)
```
  
  
  
# tricks may or may not work in lxxxcode
```c++
#pragma GCC optimize ("O2")
#pragma GCC optimize ("Ofast")

#pragma GCC optimize("03")
#pragma GCC target ("avx") or #pragma GCC target ("avx2")

//In the Solution Constructor
Solution()
{
    cin.tie(nullptr)->sync_with_stdio(false);
}
```
   
   
   
# hmmmm.... arr[i] => *(arr + i) => *(i + arr) = i[arr]
```c++
const char* hello = "hello";
for (int i = 0; i < 5; ++i)
{
    cout << i[hello];
}
```

# some modern C++ meme
```c++
auto Add(auto a, auto b)
{
    return a + b;
}

int main()
{
    auto c0 = Add(1, 2);
    auto c1 = Add(1.2, 3.4);
    auto c2 = Add(string("foo"), string("bar"));

    cout << c0 << '\n' << c1 << '\n' << c2 << '\n';
}
```
   
   
```c++
map<int, string> mp = {{1, "foo"}, {2, "bar"}};

auto node = mp.extract(1);
node.key() = 3;
mp.insert(move(node));

for (const auto& [k, v] : mp)
{
    cout << k << ' ' << v << '\n';
}
 ```



# guides I follow
[C++ Google Style Guide](https://google.github.io/styleguide/cppguide.html)  
[C++ operator overloading guides(a bit absolete, check out C++ 20 spaceship operator)](https://stackoverflow.com/questions/4421706/what-are-the-basic-rules-and-idioms-for-operator-overloading)   
[C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)  
[Godbolt](https://godbolt.org/)  
[Benchmark](https://quick-bench.com/)  


