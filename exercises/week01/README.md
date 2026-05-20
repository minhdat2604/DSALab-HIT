# Tuần 1: Tổng Quan C++ & Big-O — Bài tập

## 🎯 Mục tiêu tuần này
Hiểu Big-O, phân tích độ phức tạp, ôn tập C++ cơ bản.

---

### Bài 1: Phân tích Big-O ⭐
Xác định Big-O của 10 đoạn code C++ cho trước. Giải thích tại sao.
// 1. O(1): Truy xuất mảng
int x = arr[5]; 

// 2. O(n): Duyệt mảng 1 lần
for(int i = 0; i < n; i++) sum++; 

// 3. O(n): Duyệt mảng bước nhảy lớn (vẫn tỉ lệ thuận với n)
for(int i = 0; i < n; i += 2) sum++; 

// 4. O(n): Hai vòng lặp rời nhau (O(n) + O(n) = O(n))
for(int i = 0; i < n; i++) sum++;
for(int j = 0; j < n; j++) sum++;

// 5. O(n²): Hai vòng lặp lồng nhau
for(int i = 0; i < n; i++)
    for(int j = 0; j < n; j++) sum++;

// 6. O(n²): Vòng lặp lồng nhau phụ thuộc index (n*(n-1)/2 bước)
for(int i = 0; i < n; i++)
    for(int j = i; j < n; j++) sum++;

// 7. O(log n): Bước nhảy nhân đôi (Binary Search style)
for(int i = 1; i < n; i *= 2) sum++;

// 8. O(n log n): Vòng lặp ngoài O(n), vòng lặp trong O(log n)
for(int i = 0; i < n; i++)
    for(int j = 1; j < n; j *= 2) sum++;

// 9. O(n³): Ba vòng lặp lồng nhau
for(int i = 0; i < n; i++)
    for(int j = 0; j < n; j++)
        for(int k = 0; k < n; k++) sum++;

// 10. O(2^n): Đệ quy rẽ nhánh đôi (VD: Fibonacci thuần)
int fib(int n) {
    if (n <= 1) return n;
    return fib(n-1) + fib(n-2);
}
//Vòng lặp độc lập (nối tiếp): Lấy vòng lặp có Big-O to nhất, bỏ vòng nhỏ.
//Vòng lặp lồng nhau: Nhân Big-O của vòng ngoài với Big-O của vòng trong.
### Bài 2: Đo thời gian thực tế ⭐⭐
Dùng `chrono` đo thời gian chạy của O(n), O(n²), O(log n) với n = 1.000 → 100.000. In bảng kết quả.
#include <iostream>
#include <chrono>

void measureTime(int n) {
    auto start = std::chrono::high_resolution_clock::now();

    // Thay thế đoạn code cần đo vào đây
    volatile long long sum = 0; // Dùng volatile để compiler không tối ưu hóa bỏ vòng lặp
    for(int i = 0; i < n; i++) sum++; // Ví dụ O(n)

    auto end = std::chrono::high_resolution_clock::now();
    std::chrono::duration<double, std::milli> ms_double = end - start;
    
    std::cout << "n = " << n << " mat " << ms_double.count() << " ms\n";
}
### Bài 3: Tối ưu hàm ⭐⭐
Cho 3 hàm O(n²) — tối ưu xuống O(n) hoặc O(n log n). Chứng minh bằng cách đo thời gian.
bool hasDuplicateOptimized(const std::vector<int>& arr) {
    std::unordered_set<int> seen;
    for (int x : arr) {
        if (seen.count(x)) return true;
        seen.insert(x);
    }
    return false;
}
std::vector<int> twoSumOptimized(const std::vector<int>& arr, int target) {
    std::unordered_map<int, int> map;
    for (int i = 0; i < arr.size(); i++) {
        int complement = target - arr[i];
        if (map.count(complement)) return {map[complement], i};
        map[arr[i]] = i;
    }
    return {};
}
int maxSubArrayOptimized(const std::vector<int>& arr) {
    int current_max = arr[0], global_max = arr[0];
    for (size_t i = 1; i < arr.size(); i++) {
        current_max = std::max(arr[i], current_max + arr[i]);
        global_max = std::max(global_max, current_max);
    }
    return global_max;
}
### Bài 4: 🔥 Dự Án Mini — Big-O Benchmark Tool ⭐⭐⭐
> **Cảm hứng:** [algorithm-visualizer.org](https://algorithm-visualizer.org)

Viết chương trình **BenchmarkTool** hiển thị bảng so sánh tốc độ các thuật toán:
```
╔══════════════╦══════════╦══════════╦══════════╗
║   Thuật toán ║  n=1000  ║  n=10000 ║ n=100000 ║
╠══════════════╬══════════╬══════════╬══════════╣
║    O(1)      ║  0.001ms ║  0.001ms ║  0.001ms ║
║    O(log n)  ║  0.003ms ║  0.004ms ║  0.005ms ║
║    O(n)      ║  0.12ms  ║  1.2ms   ║  12ms    ║
║    O(n²)     ║  8ms     ║  800ms   ║  80000ms ║
╚══════════════╩══════════╩══════════╩══════════╝
```
#include <iostream>
#include <vector>
#include <chrono>
#include <iomanip>
#include <fstream>
#include <string>

using namespace std;

// === CÁC HÀM GIẢ LẬP ĐỘ PHỨC TẠP ===

void func_O1(int n) {
    volatile int x = n; 
}

void func_OlogN(int n) {
    volatile int sum = 0;
    for (int i = 1; i < n; i *= 2) sum++;
}

void func_ON(int n) {
    volatile int sum = 0;
    for (int i = 0; i < n; i++) sum++;
}

void func_ON2(int n) {
    volatile int sum = 0;
    // Tối ưu nhẹ vòng lặp ngoài để giả lập O(n^2) không tốn quá nhiều thời gian chờ thực tế
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            sum++;
        }
    }
}

// === HÀM ĐO THỜI GIAN ===
// Trả về thời gian chạy theo milliseconds
double measure(void (*func)(int), int n) {
    auto start = chrono::high_resolution_clock::now();
    func(n);
    auto end = chrono::high_resolution_clock::now();
    chrono::duration<double, std::milli> duration = end - start;
    return duration.count();
}

int main() {
    // Cấu hình N
    vector<int> sizes = {1000, 10000, 100000};
    
    // Tên các thuật toán và con trỏ hàm tương ứng
    vector<pair<string, void(*)(int)>> algorithms = {
        {"O(1)", func_O1},
        {"O(log n)", func_OlogN},
        {"O(n)", func_ON},
        {"O(n^2)", func_ON2}
    };

    // Chuẩn bị xuất ra Console & File
    ofstream outFile("benchmark.txt");
    
    // Hàm Lambda để in cả ra màn hình và file
    auto print = [&](const string& text) {
        cout << text;
        outFile << text;
    };

    // Vẽ Header của bảng ASCII
    print("╔══════════════╦═════════════╦═════════════╦═════════════╗\n");
    print("║  Thuật toán  ║   n=1000    ║   n=10000   ║  n=100000   ║\n");
    print("╠══════════════╬═════════════╬═════════════╬═════════════╣\n");

    // Chạy benchmark và in dữ liệu
    for (const auto& algo : algorithms) {
        // Cột tên thuật toán
        cout << "║ " << left << setw(12) << algo.first << " ║";
        outFile << "║ " << left << setw(12) << algo.first << " ║";

        // Cột kết quả đo đạc
        for (int n : sizes) {
            double ms = measure(algo.second, n);
            
            // Format string để ép chuẩn số thực và thêm "ms"
            char buffer[20];
            snprintf(buffer, sizeof(buffer), "%.3fms", ms);
            
            cout << " " << left << setw(11) << buffer << " ║";
            outFile << " " << left << setw(11) << buffer << " ║";
        }
        print("\n");
    }

    // Vẽ Footer của bảng ASCII
    print("╚══════════════╩═════════════╩═════════════╩═════════════╝\n");

    cout << "\n[!] Da hoan thanh benchmark. Ket qua duoc luu tai 'benchmark.txt'.\n";
    outFile.close();

    return 0;
}
**Yêu cầu:** dùng `std::chrono`, hiển thị bảng căn chỉnh đẹp, xuất ra file `benchmark.txt`.

---
📁 Tham khảo: `Chuong1_TongQuan/Chuong1_TongQuan.cpp`
