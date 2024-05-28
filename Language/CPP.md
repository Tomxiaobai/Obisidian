## 基础函数常见用法
### unorder_map用法
在最新的 C++ 版本（C++20 及以后），`unordered_map` 仍然是一个常用且强大的容器，用于存储键值对并提供高效的查找操作。它在 C++20 中没有引入新的特性，但是结合 C++20 的其他特性，如改进的范围 `for` 循环、`std::optional` 和结构化绑定等，可以使 `unordered_map` 的使用更加简洁和直观。

以下是一个全面的示例，展示了如何在最新的 C++ 版本中使用 `unordered_map`：

```cpp
#include <iostream>
#include <unordered_map>
#include <string>
#include <optional>

// 示例函数：查找键并返回可选值
std::optional<std::string> findValue(const std::unordered_map<int, std::string>& umap, int key) {
    auto it = umap.find(key);
    if (it != umap.end()) {
        return it->second;
    }
    return std::nullopt;
}

int main() {
    // 创建一个 unordered_map
    std::unordered_map<int, std::string> umap = {
        {1, "one"},
        {2, "two"},
        {3, "three"}
    };

    // 插入元素
    umap[4] = "four";
    umap.insert({5, "five"});
    umap.emplace(6, "six");

    // 访问元素
    std::cout << umap[1] << std::endl; // 输出 "one"

    try {
        std::cout << umap.at(2) << std::endl; // 输出 "two"
    } catch (const std::out_of_range& e) {
        std::cout << "Key not found" << std::endl;
    }

    // 查找元素并使用 std::optional
    auto result = findValue(umap, 3);
    if (result) {
        std::cout << "Found: " << *result << std::endl; // 输出 "Found: three"
    } else {
        std::cout << "Not found" << std::endl;
    }

    // 遍历元素
    for (const auto& [key, value] : umap) {
        std::cout << key << ": " << value << std::endl;
    }

    // 使用 auto 和结构化绑定，只关心值
    for (const auto& [_, value] : umap) {
        std::cout << value << std::endl;
    }

    // 删除元素
    umap.erase(1);

    // 检查删除结果
    for (const auto& [key, value] : umap) {
        std::cout << key << ": " << value << std::endl;
    }

    return 0;
}
```

### 关键点讲解

1. **创建和初始化**：
   ```cpp
   std::unordered_map<int, std::string> umap = {
       {1, "one"},
       {2, "two"},
       {3, "three"}
   };
   ```
   使用初始化列表初始化 `unordered_map`。

2. **插入元素**：
   ```cpp
   umap[4] = "four";
   umap.insert({5, "five"});
   umap.emplace(6, "six");
   ```
   可以使用 `[]` 操作符、`insert` 和 `emplace` 方法插入元素。

3. **访问元素**：
   ```cpp
   std::cout << umap[1] << std::endl;
   std::cout << umap.at(2) << std::endl;
   ```
   `[]` 操作符用于访问和插入元素，`at` 方法用于访问元素并在键不存在时抛出异常。

4. **查找元素**：
   ```cpp
   auto it = umap.find(3);
   if (it != umap.end()) {
       std::cout << "Found: " << it->second << std::endl;
   } else {
       std::cout << "Not found" << std::endl;
   }
   ```
   使用 `find` 方法查找元素，返回迭代器。

5. **使用 `std::optional`**：
   ```cpp
   std::optional<std::string> findValue(const std::unordered_map<int, std::string>& umap, int key) {
       auto it = umap.find(key);
       if (it != umap.end()) {
           return it->second;
       }
       return std::nullopt;
   }
   ```
   使用 `std::optional` 处理可能不存在的值。

6. **遍历元素**：
   ```cpp
   for (const auto& [key, value] : umap) {
       std::cout << key << ": " << value << std::endl;
   }

   for (const auto& [_, value] : umap) {
       std::cout << value << std::endl;
   }
   ```
   使用结构化绑定遍历 `unordered_map`，可以选择忽略键或值。

7. **删除元素**：
   ```cpp
   umap.erase(1);
   ```
   使用 `erase` 方法删除元素。

这些示例展示了如何在最新的 C++ 版本中使用 `unordered_map`，并结合现代 C++ 的新特性来编写简洁高效的代码。