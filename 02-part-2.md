# SwiftUI To‑Do List Tutorial – Part 2

> **Goal of Part 2**
> • Turn each task into a real data model (`TodoItem`) with an `id` and a `isDone` flag.
> • Let the user mark items complete with a check‑circle.
> • Support swipe‑to‑delete *and* drag‑to‑reorder.
> • Persist the array in **UserDefaults** (we’ll migrate to SwiftData in Part 3).

---

## 1  Create the `TodoItem` model

```swift
import Foundation

struct TodoItem: Identifiable, Codable, Equatable {
    let id: UUID         // unique per task
    var title: String    // task text
    var isDone: Bool     // checkmark status

    init(id: UUID = .init(), title: String, isDone: Bool = false) {
        self.id = id
        self.title = title
        self.isDone = isDone
    }
}
```

*We conform to `Codable` so the struct can still be saved in UserDefaults as JSON, and to `Equatable` so list diffing works automatically.*

---

## 2  Upgrade persistence helpers

```swift
private let kTasksKey = "tasks.v2"   // changed key so v1 data won’t clash

extension UserDefaults {
    var savedTodos: [TodoItem] {
        get {
            if let data = data(forKey: kTasksKey),
               let decoded = try? JSONDecoder().decode([TodoItem].self, from: data) {
                return decoded
            }
            return []
        }
        set {
            let data = try? JSONEncoder().encode(newValue)
            set(data, forKey: kTasksKey)
        }
    }
}
```

---

## 3  Refactor `TodoViewModel`

```swift
final class TodoViewModel: ObservableObject {
    @Published var todos: [TodoItem] {
        didSet { UserDefaults.standard.savedTodos = todos }
    }

    init() {
        todos = UserDefaults.standard.savedTodos
    }

    // Intent(s)
    func add(_ title: String) {
        let trimmed = title.trimmingCharacters(in: .whitespaces)
        guard !trimmed.isEmpty else { return }
        todos.append(TodoItem(title: trimmed))
    }

    func toggle(_ todo: TodoItem) {
        guard let idx = todos.firstIndex(of: todo) else { return }
        todos[idx].isDone.toggle()
    }

    func delete(at offsets: IndexSet) {
        todos.remove(atOffsets: offsets)
    }

    func move(from source: IndexSet, to destination: Int) {
        todos.move(fromOffsets: source, toOffset: destination)
    }
}
```

---

## 4  Update **`ContentView`** UI

```swift
struct ContentView: View {
    @State private var newTask = ""
    @EnvironmentObject var vm: TodoViewModel

    var body: some View {
        NavigationStack {
            List {
                // Input row
                HStack {
                    TextField("Add a task…", text: $newTask, onCommit: addTask)
                        .submitLabel(.done)
                    Button(action: addTask) {
                        Image(systemName: "plus.circle.fill")
                            .font(.title2)
                    }
                }

                // Task rows
                ForEach(vm.todos) { todo in
                    TaskRow(todo: todo)
                        .swipeActions {
                            Button(role: .destructive) {
                                vm.delete(at: IndexSet(integer: vm.todos.firstIndex(of: todo)!))
                            } label: {
                                Label("Delete", systemImage: "trash")
                            }
                        }
                }
                .onMove(perform: vm.move)
            }
            .navigationTitle("My Tasks")
            .toolbar { EditButton() } // enables reorder & swipe‑delete
        }
    }

    private func addTask() {
        vm.add(newTask)
        newTask = ""
    }
}
```

### TaskRow component

```swift
struct TaskRow: View {
    let todo: TodoItem
    @EnvironmentObject var vm: TodoViewModel

    var body: some View {
        HStack {
            Image(systemName: todo.isDone ? "checkmark.circle.fill" : "circle")
                .foregroundColor(todo.isDone ? .green : .secondary)
                .onTapGesture { vm.toggle(todo) }
            Text(todo.title)
                .strikethrough(todo.isDone)
                .foregroundColor(todo.isDone ? .secondary : .primary)
        }
    }
}
```

---

## 5  Run & test

1. Add several tasks.
2. Tap the circle to mark complete.
3. Enter edit mode → drag to reorder.
4. Swipe‑delete.
5. Relaunch the app—everything persists.

> **Tip**  Because UserDefaults now stores JSON, v1 data will be ignored. If you want to migrate old strings, write a one‑off in `init()` that detects `[String]` and converts it to `[TodoItem]`.

---

## What’s next? – Part 3

* Replace UserDefaults with **SwiftData** (Core Data successor).
* Add a filter to hide completed tasks.
* Support multi‑window & iPad split‑view.

Save & commit: `git commit -am "Part2 – model, reorder, persistent JSON"`.
