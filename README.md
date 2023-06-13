# How to cheese CS midterm exam  
## When the professor forgot to specify "recursion only"
![cheese_midterm](Image/CheeseMidterm.PNG?raw=true)
   
# brilliant std::string constructor
![bad std::string constructor](Image/C%2B%2BBadStringConstructor.PNG?raw=true)

# Smuggle default arguments into variadic function (credit to M.M)
```c++
struct Bar {
    int a;
    const std::source_location srce;
    Bar(int b, const std::source_location s = std::source_location::current()) : a(b), srce(s) {}
}

void Foo(Bar arg0, const auto&... args) {
  //do something
}

// integer "1" implicitly converts to Bar
Foo(1, 2, 3, 4, 5, 6);

```
   
# hmmmm.... arr[i] = *(arr + i) = *(i + arr) = i[arr]
```c++
int hello[] = {1, 2, 3};
for (int i = 0; i < 3; ++i) {
    i[hello] = 4;
}
```
  
# modern C++ duck typing meme
```c++
auto Add(const auto&... a) {
    return (... + a);
}

int main() {
    auto c0 = Add(-1, 2, -3);
    auto c1 = Add(1.2, 3.4, -3.1415926535);
    auto c2 = Add(string("unbox"), string("the"), string("cat"));
}
```
   
# extract keys from map
```c++
map<int, string> mp = {{1, "foo"}, {2, "bar"}};

auto entry = mp.extract(1);
entry.key() = 3;
mp.insert(std::move(entry));
 ```
   
# good variable names to write obfuscated codes
```
src, srce                   (source)
dst, dest                   (destination)
first, last                 (denote range [a, b])
i, j, k, x, y, z            (index for nested loops)
prev, curr, next            (parent/child nodes)
arr                         (array)
vec                         (vector)
deq                         (deque)
que                         (queue)
ls, lst                     (list)
mp                          (map/hashmap)
addr                        (address)
ptr                         (pointer)
fn, fun, func               (function)
```
  
  
# tricks may or may not work in LC
```c++
#pragma GCC optimize ("O2") or #pragma GCC optimize("O3") or #pragma GCC optimize ("Ofast")
#pragma GCC target ("avx") or #pragma GCC target ("avx2")

const auto optimize = []() {
    cin.tie(nullptr)->sync_with_stdio(false);
}();
```
  
  
# Hacky integer reader for competitive programming
```c++
//not portable
#if defined _WIN32 || defined _WIN64
inline char getchar_unlocked() { return static_cast<char>(_getchar_nolock()); }
#endif

template <std::signed_integral T>
T Read() {
    T x; bool neg = false; char c{};
    do { c = getchar_unlocked(); if (c == '-') neg = true; } while (c < '0');
    for (x = c - '0'; '0' <= (c = getchar_unlocked()); x = (x << 3) + (x << 1) + c - '0') {}
    return neg ? -x : x;
}

template <std::unsigned_integral T>
T Read() {
    T x; char c{};
    do { c = getchar_unlocked(); } while (c < '0');
    for (x = c - '0'; '0' <= (c = getchar_unlocked()); x = (x << 3) + (x << 1) + c - '0');
    return x;
}
```
  
  
# cool resources  
[Godbolt](https://godbolt.org/)  
[Benchmark](https://quick-bench.com/)  
[C++ operator overloading guides](https://stackoverflow.com/questions/4421706/what-are-the-basic-rules-and-idioms-for-operator-overloading)   
[C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)  
[Contest Events](https://clist.by/)  
[Collaboration Coding](https://ide.thecodingwizard.me/)  
[C](http://warp.povusers.org/OpenLetters/ResponseToTorvalds.html)  
![visitor counter](https://komarev.com/ghpvc/?username=evanhyd)   



