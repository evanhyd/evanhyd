# Lessons learned in a *hard way* 
Please **do not** view these as "guides"  
Open for pull requests to fix mistakes  
  
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
  
  
  
# Do not flush the output buffer for no reason
## Bad
```c++
for(int i = 0; i < N; ++i)
{
    //flushing buffer may hurt performance
    //but sometimes necessary
    std::cout << "hello" << std::endl; 
}
```
## Better, with exception of coding interactive console  
```c++
for(int i = 0; i < N; ++i)
{
    std::cout << "hello" << '\n'; 
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
  
  
  
# Exchange instead of swapping the values in move constructor
## Weird but ok  
```c++
Bar(Bar&& rhs) : Bar() //calling default constructor
{
    swap(*this, rhs);
}
```  
## Slightly Better? 
```c++
Bar(Bar&& rhs) : data_(std::exchange(rhs.data_, nullptr)), size_(std::exchange(rhs.size_, 0))
{
    //rest of the code
}
```  
  
  
  
# Be careful when manage the resources
## Bad  
```c++
T& T::operator=(const T& obj)
{
    //self assignment will lose data
    delete this->data;
    
    //throwing exception will lose old data
    this->data = new Bar(obj);
    
    return *this;
}
```
## Dubious Choice?  
```c++
T& T::operator=(const T& obj)
{
    //a potential bottleneck
    //how often does self assignment occur?
    if(this != &obj)
    {
        delete this->data;

        //throwing exception will lose old data
        this->data = new Bar(obj);
    }
    
    return *this;
}
```
## Better  
```c++
//copy by value
//provide a bit exception guarantee
T& T::operator=(T obj)
{
    std::swap(this->data, obj.data); //or replace with custom swapping function
    return *this;
}
```
  
  
  
# Do not copy/fill/move directly on the raw memory
## Bad
```c++
void Construct(T* raw_memory, int size, T& default_value)
{
    for(int i = 0; i < size; ++i)
    {
        //undefined behavior! calling copy assignment operator on uninitialized objects
        raw_memory[i] = default_value; 
    }
}
```
## Better
```c++
void Construct(T* raw_memory, int size, T& default_value)
{
    for(int i = 0; i < size; ++i)
    {
        //placement new copy construct
        operator new(raw_memory + i) T(default_value); 
    }
}
```
## Easier  
```c++
void Construct(T* raw_memory, int size, T& default_value)
{
    std::uninitialized_fill(raw_memory, raw_memory + i, default_value);
}
```

# Smuggle default arguments into variadic template (credit to M.M)
```c++
struct Bar
{
    int first_argument;
    const std::source_location srce;
    
    Bar(int a, const std::source_location s = std::source_location::current()) : first_argument(a), srce(s) {}
}

void Foo(Bar arg0, const auto&... args)
{
    //args0.srce haha
}

// 1 implicitly converts to Bar along with the source_location
Foo(1, 2, 3, 4, 5, 6);

```
  
# Rule of ???
```
3 -> 5 -> 4.5 -> 0
```
   
   
# hmmmm.... arr[i] => *(arr + i) => *(i + arr) = i[arr]
```c++
const char* hello = "hello";
for (int i = 0; i < 5; ++i)
{
    std::cout << i[hello];
}
```
  
# modern C++ duck typing
```c++
auto Add(const auto&... a)
{
    return (a + ...);
}

int main()
{
    auto c0 = Add(-1, 2, -3);
    auto c1 = Add(1.2, 3.4, -3.1415926535);
    auto c2 = Add(std::string("unbox"), std::string("the"), std::string("cat"));

    std::cout << c0 << '\n' << c1 << '\n' << c2 << '\n';
}
```
   
# extract keys from map
```c++
std::map<int, std::string> mp = {{1, "foo"}, {2, "bar"}};

auto entry = mp.extract(1);
entry.key() = 3;
mp.insert(std::move(entry));

for (const auto& [k, v] : mp)
{
    std::cout << k << ' ' << v << '\n';
}
 ```
 
 # concise variable names
 ```c++
 static thread_local inline volatile constexpr const unsigned long long int * const p = nullptr;
 ```
   
   
# brilliant std::string constructor
![bad std::string constructor](https://github.com/evanhyd/evanhyd/blob/main/Image/C%2B%2BBadStringConstructor.PNG?raw=true)
   
   
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
  
  
# tricks may or may not work in LC
```c++
#pragma GCC optimize ("O2")
#pragma GCC optimize ("Ofast")

#pragma GCC optimize("03")
#pragma GCC target ("avx") or #pragma GCC target ("avx2")

//Replace the Solution constructor with
Solution()
{
    cin.tie(nullptr)->sync_with_stdio(false);
}
```
  
  
# Hacky integer reader for competitive programming (stolen and modified from DMOJ)   
```c++
//not portable
#if defined _WIN32 || defined _WIN64
inline char getchar_unlocked() { return static_cast<char>(_getchar_nolock()); }
#endif

template <std::signed_integral T>
T Read()
{
    T x; bool neg = false; char c{};
    do { c = getchar_unlocked(); if (c == '-') neg = true; } while (c < '0');
    for (x = c - '0'; '0' <= (c = getchar_unlocked()); x = (x << 3) + (x << 1) + c - '0') {}
    return neg ? -x : x;
}

template <std::unsigned_integral T>
T Read()
{
    T x; char c{};
    do { c = getchar_unlocked(); } while (c < '0');
    for (x = c - '0'; '0' <= (c = getchar_unlocked()); x = (x << 3) + (x << 1) + c - '0');
    return x;
}
```
  
  
# guides I follow
[C++ Google Style Guide](https://google.github.io/styleguide/cppguide.html)  
[C++ operator overloading guides(a bit absolete, check out C++ 20 spaceship operator)](https://stackoverflow.com/questions/4421706/what-are-the-basic-rules-and-idioms-for-operator-overloading)   
[C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)  
[Godbolt](https://godbolt.org/)  
[Benchmark](https://quick-bench.com/)  



# fun sites
[NO LIFE](https://clist.by/)  
[c iS tHe bEsT lAnGuAgE, bEcAuSe lInUs tOrVaLdS sAiD sO](http://warp.povusers.org/OpenLetters/ResponseToTorvalds.html)  
[ONLINE COLLABORATIVE CODING](https://ide.thecodingwizard.me/)
![visitor counter](https://komarev.com/ghpvc/?username=evanhyd)



