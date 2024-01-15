#include <iostream>
#include <fstream>
#include <vector>
#include <algorithm>

using namespace std;

// Structure to represent a contact
struct Contact {
    string name;
    string phoneNumber;
    string email;
    // Add any additional fields as needed
};

// Linked List Node
struct ListNode {
    Contact data;
    ListNode* next;
    ListNode(Contact contact) : data(contact), next(nullptr){}
};

// Linked List Class
class LinkedList {
public:
    ListNode* head;

    LinkedList() : head(nullptr) {}

    void insert(Contact contact) {
        ListNode* newNode = new ListNode(contact);
        newNode->next = head;
        head = newNode;
    }

    void display() {
        ListNode* current = head;
        while (current != nullptr) {
            cout << "Name: " << current->data.name << "\n";
            cout << "Phone Number: " << current->data.phoneNumber << "\n";
            cout << "Email: " << current->data.email << "\n";
            cout << "-------------------------\n";
            current = current->next;
        }
    }
};

// Heap Class
class MaxHeap {
private:
    vector<Contact> heap;

    void heapifyUp(int index) {
        while (index > 0) {
            int parent = (index - 1) / 2;
            if (heap[index].name > heap[parent].name) {
                swap(heap[index], heap[parent]);
                index = parent;
            } else {
                break;
            }
        }
    }

    void heapifyDown(int index) {
        int leftChild = 2 * index + 1;
        int rightChild = 2 * index + 2;
        int largest = index;

        if (leftChild < heap.size() && heap[leftChild].name > heap[largest].name) {
            largest = leftChild;
        }

        if (rightChild < heap.size() && heap[rightChild].name > heap[largest].name) {
            largest = rightChild;
        }

        if (largest != index) {
            swap(heap[index], heap[largest]);
            heapifyDown(largest);
        }
    }

public:
    void insert(Contact contact) {
        heap.push_back(contact);
        heapifyUp(heap.size() - 1);
    }

    Contact extractMax() {
        if (heap.empty()) {
            cerr << "Heap is empty!" << endl;
            exit(1);
        }

        Contact maxContact = heap[0];
        swap(heap[0], heap.back());
        heap.pop_back();
        heapifyDown(0);

        return maxContact;
    }

    void display() {
        for (const Contact& contact : heap) {
            cout << "Name: " << contact.name << "\n";
            cout << "Phone Number: " << contact.phoneNumber << "\n";
            cout << "Email: " << contact.email << "\n";
            cout << "-------------------------\n";
        }
    }

    bool isEmpty() const {
        return heap.empty();
    }
};

// Stack Class
class Stack {
private:
    vector<Contact> stack;

public:
    void push(Contact contact) {
        stack.push_back(contact);
    }

    void pop() {
        if (!stack.empty()) {
            stack.pop_back();
        } else {
            cerr << "Stack is empty!" << endl;
            exit(1);
        }
    }

    Contact top() {
        if (!stack.empty()) {
            return stack.back();
        } else {
            cerr << "Stack is empty!" << endl;
            exit(1);
        }
    }

    bool isEmpty() {
        return stack.empty();
    }
};

// Queue Class
class Queue {
private:
    vector<Contact> queue;

public:
    void enqueue(Contact contact) {
        queue.push_back(contact);
    }

    void dequeue() {
        if (!queue.empty()) {
            queue.erase(queue.begin());
        } else {
            cerr << "Queue is empty!" << endl;
            exit(1);
        }
    }

    Contact front() {
        if (!queue.empty()) {
            return queue.front();
        } else {
            cerr << "Queue is empty!" << endl;
            exit(1);
        }
    }

    bool isEmpty() {
        return queue.empty();
    }
};

// Sorting Algorithms
template <typename T>
void quickSort(vector<T>& arr, int low, int high) {
    if (low < high) {
        int pivotIndex = partition(arr, low, high);
        quickSort(arr, low, pivotIndex - 1);
        quickSort(arr, pivotIndex + 1, high);
    }
}

template <typename T>
int partition(vector<T>& arr, int low, int high) {
    T pivot = arr[high];
    int i = low - 1;

    for (int j = low; j < high; ++j) {
        if (arr[j].name <= pivot.name) {
            ++i;
            swap(arr[i], arr[j]);
        }
    }

    swap(arr[i + 1], arr[high]);
    return i + 1;
}

template <typename T>
void displayArray(const vector<T>& arr) {
    for (const T& element : arr) {
        cout << "Name: " << element.name << "\n";
        cout << "Phone Number: " << element.phoneNumber << "\n";
        cout << "Email: " << element.email << "\n";
        cout << "-------------------------\n";
    }
}

int main() {
    LinkedList linkedList;
    MaxHeap maxHeap;
    Stack undoStack;
    Queue operationQueue;
    vector<Contact> contacts;

    // Sample contacts
    /*contacts.push_back({"Abdullah", "1234567", "john.doe@email.com"});
    contacts.push_back({"usman", "232323", "jane.smith@email.com"});
    contacts.push_back({"kashan", "", "bob.johnson@email.com"});*/

    // Initialize linked list, heap, and array of contacts
    for (const Contact& contact : contacts) {
        linkedList.insert(contact);
        maxHeap.insert(contact);
    }

    // Sort array of contacts
    quickSort(contacts, 0, contacts.size()- 1);

    int choice;

    do {
        cout << "\nContact Management System\n";
        cout << "1. Add Contact\n";
        cout << "2. Display Contacts\n";
        cout << "3. Undo Last Operation\n";
        cout << "0. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;

        switch (choice) {
            case 1: {
                Contact newContact;
                cout << "Enter Name: ";
                cin.ignore();
                getline(cin, newContact.name);
                cout << "Enter Phone Number: ";
                cin >> newContact.phoneNumber;
                cout << "Enter Email: ";
                cin >> newContact.email;

                // Add to linked list
                linkedList.insert(newContact);

                // Add to heap
                maxHeap.insert(newContact);

                // Add to array
                contacts.push_back(newContact);

                // Push operation to undo stack
                undoStack.push(newContact);

                cout << "Contact added successfully!\n";
                break;
            }
            case 2:
                // Display contacts using linked list
                cout << "Contacts from Linked List:\n";
                linkedList.display();

                // Display contacts using heap
                cout << "\nContacts from Heap:\n";
                maxHeap.display();

                // Display contacts using array
                cout << "\nContacts from Array:\n";
                displayArray(contacts);
                break;
            case 3:
                // Undo last operation
                if (!undoStack.isEmpty()) {
                    Contact lastOperation = undoStack.top();
                    undoStack.pop();

                    // Remove from linked list
                    // Note: This is a simple linear search; a more efficient implementation could use additional data structures.
                    ListNode* current = linkedList.head;
                    ListNode* prev = nullptr;
                    while (current != nullptr) {
                        if (current->data.name == lastOperation.name) {
                            if (prev == nullptr) {
                                linkedList.head = current->next;
                            } else {
                                prev->next = current->next;
                            }
                            delete current;
                            break;
                        }
                        prev = current;
                        current = current->next;
                    }

                    // Remove from heap
                    // Note: This is a simple linear search; a more efficient implementation could use additional data structures.
                    vector<Contact> tempHeap;
                    while (!maxHeap.isEmpty()) {
                        Contact contact = maxHeap.extractMax();
                        if (contact.name != lastOperation.name) {
                            tempHeap.push_back(contact);
                        }
                    }
                    for (const Contact& contact : tempHeap) {
                        maxHeap.insert(contact);
                    }

                    // Remove from array
                    // Note: This is a simple linear search; a more efficient implementation could use additional data structures.
                    vector<Contact> tempArray;
                    for (const Contact& contact : contacts) {
                        if (contact.name != lastOperation.name) {
                            tempArray.push_back(contact);
                        }
                    }
                    contacts = tempArray;

                    cout << "Last operation undone successfully!\n";
                } else {
                    cout << "No operations to undo.\n";
                }
                break;
            case 0:
                cout << "Exiting the Contact Management System.\n";
                break;
            default:
                cout << "Invalid choice. Please try again.\n";
        }
    } while (choice != 0);

    return 0;
}

