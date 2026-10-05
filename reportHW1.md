# 資料結構 Homework 1 作業報告

## 基本資料
學號：41443124
姓名：金廷臻

## 開發與測試環境
作業系統：Windows 11 / WSL2 (Ubuntu)
開發工具：VS Code
編譯器：GCC (g++)

## 程式設計說明與邏輯

### Problem 1: Ackermann 函數

**1. 遞迴版本**
這個版本比較簡單，主要是直接照著題目給的數學定義去寫。寫的時候直接用 if-else 分成三個判斷式：m=0、n=0，以及其他 (m>0 且 n>0) 的情況。雖然寫起來很直觀，但只要數字大一點點，遞迴深度就會爆表（Stack Overflow）。
```cpp
int ackermannRecursive(int m, int n) {
    if (m == 0) {
        return n + 1;
    } else if (n == 0) {
        return ackermannRecursive(m - 1, 1);
    } else {
        return ackermannRecursive(m - 1, ackermannRecursive(m, n - 1));
    }
}
```

**2. 非遞迴版本**
非遞迴版本稍微卡了一下。因為不能呼叫函式本身，所以我改用 C++ 內建的 std::stack 來模擬系統遞迴時的堆疊行為。最麻煩的地方是第三個條件 `A(m-1, A(m, n-1))` 是巢狀的，所以我必須先把外層的 `m-1` push 進堆疊，再把內層的 `m` push 進去，這樣 pop 的時候才會先計算內層的結果。
```cpp
int ackermannNonRecursive(int m, int n) {
    std::stack<int> s;
    s.push(m);

    while (!s.empty()) {
        m = s.top();
        s.pop();

        if (m == 0) {
            n = n + 1;
        } else if (n == 0) {
            s.push(m - 1);
            n = 1;
        } else {
            // 處理巢狀遞迴：先推外層參數，再推內層參數
            s.push(m - 1); 
            s.push(m);     
            n = n - 1;
        }
    }
    return n;
}
```

### Problem 2: Powerset (冪集)

這個題目是要印出所有的子集合，我用的是遞迴與回溯（Backtracking）的概念來解。
邏輯上就是對陣列裡面的每一個元素進行「選」或「不選」的抉擇。當指標走到陣列最後面的時候（代表每個元素都決定過一輪了），就把當下陣列裡存的組合印出來。在程式碼實作上，我用 `std::vector` 來暫存目前選到的元素，透過 `push_back` 放入元素，跑完遞迴後再用 `pop_back` 拿出來還原狀態。
```cpp
void getPowerset(const std::vector<char>& S, size_t index, std::vector<char>& current) {
    // 終止條件：所有元素都決定過了
    if (index == S.size()) {
        std::cout << "(";
        for (size_t i = 0; i < current.size(); ++i) {
            std::cout << current[i] << (i + 1 < current.size() ? ", " : "");
        }
        std::cout << ")\n";
        return;
    }

    // 分支一：不選擇當前元素 S[index]
    getPowerset(S, index + 1, current);

    // 分支二：選擇當前元素 S[index] 加入子集合
    current.push_back(S[index]);
    getPowerset(S, index + 1, current);

    // 回溯（Backtrack）：還原狀態以便回到上一層
    current.pop_back();
}
```

## 執行結果
（請在這裡附上你在 VS Code 終端機執行的畫面截圖，可以直接截圖後貼上來，GitHub 會自動產生圖片連結）

## 心得與討論
這次作業雖然只有兩題，但實際去寫非遞迴的 Ackermann 時，才體會到作業系統在背後處理遞迴呼叫有多麻煩。用 stack 模擬的時候，一開始不小心 push 順序搞錯，導致無窮迴圈，後來重新在紙上畫了一次堆疊狀態才搞懂。Powerset 的部分則是讓我更熟悉 C++ STL vector 的操作和遞迴回溯法的概念。下週就要考資料結構期中考了，剛好拿這兩題當作對遞迴和時間複雜度的複習。
