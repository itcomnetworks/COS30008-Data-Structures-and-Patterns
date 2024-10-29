### Exercise 1: Exploring Memory Types

**Objective:** Understand how different types of memory are utilized in a C++ program.

**Instructions:**
1. Analyze the following code snippet that demonstrates different memory types:
   - Static variable
   - Constant variable
   - Local variable
   - Dynamically allocated memory (heap)

2. Modify the code to:
   - Print the addresses of each variable.
   - Change the value of the static and constant variables and observe the behavior.

#### Example Code:

```cpp
#include <iostream>

static int staticVar = 10; // Static variable

const int constVar = 20; // Constant variable

void displayMemory() {
    int localVar = 30; // Local variable
    int* heapVar = new int(40); // Dynamically allocated variable

    std::cout << "Static Variable Address: " << &staticVar << " Value: " << staticVar << std::endl;
    std::cout << "Constant Variable Address: " << &constVar << " Value: " << constVar << std::endl;
    std::cout << "Local Variable Address: " << &localVar << " Value: " << localVar << std::endl;
    std::cout << "Heap Variable Address: " << heapVar << " Value: " << *heapVar << std::endl;

    delete heapVar; // Free heap memory
}

int main() {
    displayMemory();
    
    // Attempt to modify static and constant variables
    staticVar = 15; // Allowed
    // constVar = 25; // Uncommenting this line should cause a compilation error

    std::cout << "Modified Static Variable Value: " << staticVar << std::endl;

    return 0;
}
```

### Questions:
- What happens if you uncomment the line attempting to modify `constVar`?
- Why is it important to manage heap memory using `delete`?
- How does the scope of `localVar` affect its accessibility?

---

### Exercise 2: Implementing Reference Counting

**Objective:** Create a class that uses reference counting for memory management.

**Instructions:**
1. Implement a class named `RefCountedResource` that manages an integer resource with reference counting.
2. Include:
   - A constructor that initializes the resource.
   - A copy constructor that increments the reference count.
   - An assignment operator that handles self-assignment and decrements the old reference count.
   - A destructor that releases resources when the count reaches zero.

3. Write a main function to demonstrate creating, copying, and assigning instances of `RefCountedResource`.

#### Example Code:

```cpp
#include <iostream>

class RefCountedResource {
private:
    int* resource;
    int* refCount;

public:
    RefCountedResource(int value) {
        resource = new int(value);
        refCount = new int(1); // Initialize reference count to 1
    }

    RefCountedResource(const RefCountedResource& other) {
        resource = other.resource;
        refCount = other.refCount;
        (*refCount)++; // Increment reference count
    }

    RefCountedResource& operator=(const RefCountedResource& other) {
        if (this != &other) {
            if (--(*refCount) == 0) {
                delete resource; // Free old resource
                delete refCount; // Free old refCount
            }
            resource = other.resource;
            refCount = other.refCount;
            (*refCount)++; // Increment new reference count
        }
        return *this;
    }

    ~RefCountedResource() {
        if (--(*refCount) == 0) {
            delete resource; // Free resource if no references left
            delete refCount; // Free reference count
        }
    }

    void display() const {
        std::cout << "Resource Value: " << *resource 
                  << ", Reference Count: " << *refCount << std::endl;
    }
};

int main() {
    RefCountedResource res1(42);
    res1.display();

    RefCountedResource res2 = res1; // Copy constructor called
    res2.display();

    res1.display(); // Reference count should reflect current state

    return 0;
}
```

### Questions:
- What happens to the reference count when you create `res2`?
- Modify the code to add a method that returns the current reference count.
- What would happen if you forgot to decrement the reference count in the destructor?

---

### Exercise 3: Fixing Memory Leaks

**Objective:** Identify and fix memory leaks in a given code snippet.

**Instructions:**
1. Analyze the following code snippet which contains intentional memory leaks due to missing `delete` statements after dynamic allocations.

```cpp
#include <iostream>

void createLeak() {
    int* leak = new int(42); // Memory leak here
}

int main() {
    for (int i = 0; i < 10; ++i) {
        createLeak(); // Each call leaks memory
    }
    
    return 0;
}
```

2. Modify this code to ensure that all dynamically allocated memory is properly released before exiting.

#### Expected Fix:

```cpp
#include <iostream>

void createLeak(int*& leak) { // Pass by reference to modify pointer in main
    leak = new int(42; // Allocate memory
}

int main() {
    int* leakPtr = nullptr;

    for (int i = 0; i < 10; ++i) {
        createLeak(leakPtr); // Allocate memory each time
        delete leakPtr;      // Free memory immediately after use
        leakPtr = nullptr;   // Reset pointer to avoid dangling pointer
    }
    
    return 0;
}
```

### Questions:
- How does passing by reference help in managing dynamic memory?
- What are some tools or techniques you can use to detect memory leaks in C++?
- Why is it important to set pointers to `nullptr` after deletion?