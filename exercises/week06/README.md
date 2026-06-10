# Tuần 6: Danh Sách Liên Kết Đơn — Bài tập

## 🎯 Mục tiêu tuần này
Cài đặt Singly Linked List, thành thạo con trỏ, thêm/xóa/duyệt.

---

### Bài 1: Cài đặt Linked List ⭐⭐
Cài đặt đầy đủ: thêm đầu, thêm cuối, xóa đầu, xóa cuối, xóa theo giá trị, tìm kiếm, in danh sách.

#include <iostream>
using namespace std;

// Định nghĩa cấu trúc của một ô nhớ (Node)
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {} // Hàm khởi tạo Node
};

// Đóng gói danh sách liên kết vào một Class để quản lý gọn gàng
class LinkedList {
private:
    Node* head; // Con trỏ quản lý đầu danh sách

public:
    // Hàm khởi tạo danh sách rỗng
    LinkedList() {
        head = nullptr;
    }

   // Hàm hủy để tự động giải phóng bộ nhớ khi tắt chương trình (Tránh leak bộ nhớ)
    ~LinkedList() {
        while (head != nullptr) {
            Node* temp = head;
            head = head->next;
            delete temp;
        }
    }

   // 1. Thêm vào đầu danh sách
    void insertHead(int val) {
        Node* newNode = new Node(val);
        newNode->next = head;
        head = newNode;
    }

   // 2. Thêm vào cuối danh sách
    void insertTail(int val) {
        Node* newNode = new Node(val);
        if (head == nullptr) {
            head = newNode;
            return;
        }
        Node* temp = head;
        while (temp->next != nullptr) {
            temp = temp->next;
        }
        temp->next = newNode;
    }

   // 3. Xóa phần tử đầu tiên
    void deleteHead() {
        if (head == nullptr) {
            cout << "Danh sach rong, khong the xoa!\n";
            return;
        }
        Node* temp = head;
        head = head->next;
        delete temp;
    }

   // 4. Xóa phần tử cuối cùng
    void deleteTail() {
        if (head == nullptr) {
            cout << "Danh sach rong, khong the xoa!\n";
            return;
        }
        // Trường hợp danh sách chỉ có đúng 1 phần tử
        if (head->next == nullptr) {
            delete head;
            head = nullptr;
            return;
        }
        // Trường hợp có từ 2 phần tử trở lên
        Node* temp = head;
        while (temp->next->next != nullptr) {
            temp = temp->next;
        }
        delete temp->next;
        temp->next = nullptr;
    }

  // 5. Xóa phần tử theo giá trị xuất hiện đầu tiên
    void deleteValue(int val) {
        if (head == nullptr) return;
        
   // Nếu giá trị cần xóa nằm ngay ở vị trí đầu tiên
        if (head->data == val) {
            deleteHead();
            return;
        }
        
   Node* temp = head;
        while (temp->next != nullptr && temp->next->data != val) {
            temp = temp->next;
        }
        
   // Nếu tìm thấy Node có giá trị trùng khớp
        if (temp->next != nullptr) {
            Node* nodeToDelete = temp->next;
            temp->next = temp->next->next; // Bỏ qua Node cần xóa
            delete nodeToDelete;           // Thu hồi bộ nhớ
        } else {
            cout << "Khong tim thay gia tri " << val << " de xoa.\n";
        }
    }

  // 6. Tìm kiếm một giá trị (Trả về true/false)
    bool search(int val) {
        Node* temp = head;
        while (temp != nullptr) {
            if (temp->data == val) return true;
            temp = temp->next;
        }
        return false;
    }

  // 7. In danh sách ra màn hình
    void printList() {
        Node* temp = head;
        if (temp == nullptr) {
            cout << "EMPTY LIST\n";
            return;
        }
        while (temp != nullptr) {
            cout << temp->data << " -> ";
            temp = temp->next;
        }
        cout << "NULL\n";
    }
};

// ============================================================================
// HÀM MAIN KIỂM TRA TOÀN BỘ CHỨC NĂNG
// ============================================================================
int main() {
    LinkedList list;

   cout << "--- Thu nghiem Them dau va Them cuoi ---\n";
    list.insertHead(10);
    list.insertHead(20); // Danh sách hiện tại: 20 -> 10 -> NULL
    list.insertTail(30);
    list.insertTail(40); // Danh sách hiện tại: 20 -> 10 -> 30 -> 40 -> NULL
    list.printList();

  cout << "\n--- Thu nghiem Xoa Dau va Xoa Cuoi ---\n";
    list.deleteHead(); // Xóa số 20
    list.printList();
    list.deleteTail(); // Xóa số 40
    list.printList();

  cout << "\n--- Thu nghiem Tim Kiem ---\n";
    cout << "Tim so 30: " << (list.search(30) ? "Tim thay" : "Khong tim thay") << "\n";
    cout << "Tim so 99: " << (list.search(99) ? "Tim thay" : "Khong tim thay") << "\n";

  cout << "\n--- Thu nghiem Xoa Theo Gia Tri ---\n";
    list.deleteValue(10); // Xóa số 10 ở giữa mảng
    list.printList();
    list.deleteValue(30); // Xóa tiếp số 30 (danh sách sẽ rỗng)
    list.printList();

   return 0;
}
### Bài 2: Đảo ngược danh sách ⭐⭐
Đảo ngược Linked List bằng 2 cách: iterative (3 con trỏ) và recursive. So sánh.

#include <iostream>
using namespace std;

struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class LinkedList {
private:
    Node* head;

  // Hàm bổ trợ đệ quy (Private Helper) - Được giấu kín bên trong class
    Node* reverseRecursiveHelper(Node* current) {
        // Điều kiện dừng: nếu danh sách rỗng hoặc đi đến Node cuối cùng
        if (current == nullptr || current->next == nullptr) {
            return current; 
        }

  // Tiếp tục đi sâu xuống cuối danh sách
        Node* newHead = reverseRecursiveHelper(current->next);

   // Đảo chiều mũi tên liên kết giữa hai Node kế nhau
        current->next->next = current;
        current->next = nullptr;

   return newHead; // Trả về Head mới (chính là Node cuối cùng cũ ban đầu)
    }

public:
    LinkedList() : head(nullptr) {}

   // Hàm thêm phần tử vào cuối đề tạo danh sách test
    void insertTail(int val) {
        Node* newNode = new Node(val);
        if (head == nullptr) {
            head = newNode;
            return;
        }
        Node* temp = head;
        while (temp->next != nullptr) {
            temp = temp->next;
        }
        temp->next = newNode;
    }

   // ------------------------------------------------------------------------
    // CÁCH 1: ĐẢO NGƯỢC DÙNG VÒNG LẶP (ITERATIVE - 3 CON TRỎ)
    // ------------------------------------------------------------------------
    void reverseIterative() {
        Node* prev = nullptr;
        Node* curr = head;
        Node* nextNode = nullptr;

   while (curr != nullptr) {
            nextNode = curr->next; // 1. Giữ lại địa chỉ Node tiếp theo
            curr->next = prev;     // 2. Bẻ ngược con trỏ về phía trước
            prev = curr;           // 3. Di chuyển prev lên một bước
            curr = nextNode;       // 4. Di chuyển curr lên một bước
        }
        head = prev; // Cập nhật lại head mới cho Class
    }

  // ------------------------------------------------------------------------
    // CÁCH 2: ĐẢO NGƯỢC DÙNG ĐỆ QUY (RECURSIVE)
    // ------------------------------------------------------------------------
    void reverseRecursive() {
        // Gọi hàm helper và cập nhật kết quả vào biến head
        head = reverseRecursiveHelper(head);
    }

  // Hàm in danh sách
    void printList() {
        Node* temp = head;
        if (temp == nullptr) {
            cout << "EMPTY LIST\n";
            return;
        }
        while (temp != nullptr) {
            cout << temp->data << " -> ";
            temp = temp->next;
        }
        cout << "NULL\n";
    }
};

// ============================================================================
// HÀM MAIN CHẠY THỬ NGHIỆM
// ============================================================================
int main() {
    LinkedList list1;
    list1.insertTail(1);
    list1.insertTail(2);
    list1.insertTail(3);
    list1.insertTail(4);

  cout << "Danh sach ban dau: ";
    list1.printList();

  cout << "\n--- Chay cach 1: Vong lap (Iterative) ---\n";
    list1.reverseIterative();
    list1.printList();

  cout << "\n--- Chay cach 2: De quy (Recursive) ---\n";
    // Đảo ngược thêm 1 lần nữa bằng đệ quy để nó quay về trạng thái ban đầu
    list1.reverseRecursive();
    list1.printList();

  return 0;
}

### Bài 3: Phát hiện vòng lặp ⭐⭐⭐
Cài đặt Floyd's Cycle Detection (slow/fast pointer). Tìm điểm bắt đầu vòng lặp.

#include <iostream>
using namespace std;

struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class LinkedList {
private:
    Node* head;

public:
    LinkedList() : head(nullptr) {}

  // Hàm thêm vào cuối để tạo danh sách ban đầu
    void insertTail(int val) {
        Node* newNode = new Node(val);
        if (head == nullptr) {
            head = newNode;
            return;
        }
        Node* temp = head;
        while (temp->next != nullptr) {
            temp = temp->next;
        }
        temp->next = newNode;
    }

   // ------------------------------------------------------------------------
    // HÀM ĐẶC BIỆT: Tự tạo vòng lặp giả lập để chạy thử nghiệm (Test)
    // Nối Node cuối cùng ngược về Node tại vị trí 'index' (bắt đầu từ 0)
    // ------------------------------------------------------------------------
    void createCycle(int index) {
        if (head == nullptr) return;

   Node* temp = head;
        Node* targetNode = nullptr;
        int count = 0;

  // Duyệt đến cuối mảng, đồng thời ghi nhớ Node tại vị trí index
        while (temp->next != nullptr) {
            if (count == index) {
                targetNode = temp;
            }
            temp = temp->next;
            count++;
        }
        // Check nốt phần tử cuối cùng nếu index nằm ở cuối
        if (count == index) targetNode = temp;

   // Bẻ con trỏ của Node cuối trỏ ngược về targetNode -> Tạo thành vòng lặp!
        if (targetNode != nullptr) {
            temp->next = targetNode;
            cout << "-> Da tao gia lap vong lap quay ve Node chua so: " << targetNode->data << "\n";
        }
    }

   // ------------------------------------------------------------------------
    // BÀI 3: THUẬT TOÁN FLOYD'S CYCLE DETECTION (RÙA VÀ THỎ)
    // ------------------------------------------------------------------------
    void detectAndFindLoop() {
        if (head == nullptr || head->next == nullptr) {
            cout << "Danh sach rong hoac chi co 1 phan tu, khong co vong lap.\n";
            return;
        }

   Node* slow = head; // Rùa (Đi 1 bước)
        Node* fast = head; // Thỏ (Đi 2 bước)
        bool hasCycle = false;

  // Bước 1: Phát hiện xem có vòng lặp hay không
        while (fast != nullptr && fast->next != nullptr) {
            slow = slow->next;       // Rùa đi 1 ô
            fast = fast->next->next; // Thỏ nhảy 2 ô
            
   if (slow == fast) {      // Thỏ đuổi kịp Rùa chứng tỏ CÓ VÒNG LẶP
                hasCycle = true;
                break;
            }
        }

  if (!hasCycle) {
            cout << "Ket qua: Danh sach AN TOAN, khong co vong lap.\n";
            return;
        }

   cout << "Ket qua: PHAT HIEN VONG LAP THANH CONG!\n";

  // Bước 2: Tìm điểm bắt đầu của vòng lặp
        // Đưa Rùa về lại vạch xuất phát (Head), Thỏ đứng im tại điểm gặp nhau cũ
        slow = head;
        while (slow != fast) {
            slow = slow->next; // Bây giờ cả hai cùng đi đều 1 ô/lượt
            fast = fast->next;
        }

   // Khi gặp nhau lần 2, vị trí đó chính là điểm bắt đầu vòng lặp
        cout << "=> Vong lap bat dau tai Node co gia tri = " << slow->data << "\n";
    }

  // ------------------------------------------------------------------------
    // HÀM PHỤ: Bẻ gãy vòng lặp để giải phóng bộ nhớ an toàn (Tránh treo máy)
    // ------------------------------------------------------------------------
    void breakCycle() {
        Node* slow = head;
        Node* fast = head;
        while (fast != nullptr && fast->next != nullptr) {
            slow = slow->next;
            fast = fast->next->next;
            if (slow == fast) {
                slow = head;
                if (slow == fast) {
                    while (fast->next != slow) fast = fast->next;
                } else {
                    while (slow->next != fast->next) {
                        slow = slow->next;
                        fast = fast->next;
                    }
                }
                fast->next = nullptr; // Bẻ gãy mối xích vòng lặp
                return;
            }
        }
    }
};

// ============================================================================
// HÀM MAIN CHẠY THỬ NGHIỆM
// ============================================================================
int main() {
    LinkedList list;
    
  // Tạo danh sách tuyến tính thông thường: 10 -> 20 -> 30 -> 40 -> 50 -> NULL
    list.insertTail(10);
    list.insertTail(20);
    list.insertTail(30);
    list.insertTail(40);
    list.insertTail(50);

  cout << "--- Thử nghiem 1: Khi danh sach chua co vong lap ---\n";
    list.detectAndFindLoop();

  cout << "\n--- Thử nghiem 2: Tiến hanh tao vong lặp ---\n";
    // Nối số 50 ngược về vị trí index 2 (chính là số 30)
    // Sơ đồ lúc này: 10 -> 20 -> 30 -> 40 -> 50 -> quay lại 30
    list.createCycle(2); 
    
  list.detectAndFindLoop();

  // Dọn dẹp mảng trước khi tắt chương trình
    list.breakCycle(); 
    return 0;
}

### Bài 4: 🔥 Dự Án Mini — Lịch Sử Trình Duyệt ⭐⭐⭐
> **Cảm hứng:** BaiTapTongHop — Lịch sử trình duyệt (DSALab)

Mô phỏng lịch sử duyệt web bằng Singly Linked List:
```
=== TRÌNH DUYỆT WEB (Linked List) ===
> visit google.com
> visit facebook.com  
> visit youtube.com
> back
← Quay lại: facebook.com
> back
← Quay lại: google.com
> forward
→ Tiến tới: facebook.com
> history
📋 Lịch sử: google.com → facebook.com → youtube.com
                                ↑ (đang ở đây)
```
**Yêu cầu:** hỗ trợ visit, back, forward, history, clear, tối đa 50 trang trong lịch sử.

---
📁 Tham khảo: `Chuong3_DanhSachLienKet/Chuong3_DanhSachLienKet.cpp`
