# SwiftUI To‑Do List Tutorial – Part 3

> **Goal of Part 3**
>
> 1. Add a **NavigationStack** (if you skipped it earlier) so each row can push a detail screen.
> 2. Create `TodoDetailView` that lets the user **edit the title** and **toggle completion**.
> 3. Update the `TodoViewModel` so changes propagate back to the list *and* persist to UserDefaults.
>
> When finished you’ll have full CRUD (create, read, update, delete) for a simple to‑do list.

---

## 1  Wrap the list in `NavigationStack`

If you followed Part 1 exactly you already have a stack; otherwise adjust `ContentView`:

```swift
struct ContentView: View {
    @EnvironmentObject var vm: TodoViewModel
    @State private var newTask = ""

    var body: some View {
        NavigationStack {                     // ← NEW
            VStack(spacing: 12) {
                inputBar
                todoList
            }
            .navigationTitle("My Tasks")
        }
    }

    // Extracted sub‑views for clarity
    private var inputBar: some View { /* unchanged */ }
    private var todoList: some View {
        List {
            ForEach(vm.items) { item in
                NavigationLink {
                    TodoDetailView(todo: item) // push detail
                } label: {
                    TaskRow(todo: item)
                }
            }
            .onDelete(perform: vm.delete)
            .onMove(perform: vm.move)
        }
    }
}
```

### View‑model rename

In Part 2 we renamed `tasks` → `items` and `TodoItem` now has `id` + `isDone`. Ensure `vm.items` is the source of truth.

---

## 2  Create `TodoDetailView`

```swift
struct TodoDetailView: View {
    @EnvironmentObject var vm: TodoViewModel
    @State private var draft: TodoItem

    // Pass the item to edit in the initializer
    init(todo: TodoItem) {
        _draft = State(initialValue: todo)
    }

    var body: some View {
        Form {
            Section("Task") {
                TextField("Title", text: $draft.title)
                Toggle("Completed", isOn: $draft.isDone)
            }
        }
        .navigationTitle("Edit Task")
        .toolbar {
            ToolbarItem(placement: .navigationBarTrailing) {
                Button("Save", action: saveChanges)
            }
        }
    }

    private func saveChanges() {
        vm.update(draft)            // new intent in the VM
    }
}
```

> We keep a **local `@State draft`** so the user can cancel with the system back‑swipe without mutating shared data until “Save”.

### Preview

```swift
#Preview {
    NavigationStack {
        TodoDetailView(todo: TodoItem(title: "Sample", isDone: false))
            .environmentObject(TodoViewModel())
    }
}
```

---

## 3  Update `TodoViewModel`

```swift
final class TodoViewModel: ObservableObject {
    @Published var items: [TodoItem] {
        didSet { persist() }
    }

    // MARK: – Init & persistence
    private let key = "tasks.v2"  // new key after the model upgrade
    init() {
        if let data = UserDefaults.standard.data(forKey: key),
           let decoded = try? JSONDecoder().decode([TodoItem].self, from: data) {
            items = decoded
        } else {
            items = []
        }
    }
    private func persist() {
        if let encoded = try? JSONEncoder().encode(items) {
            UserDefaults.standard.set(encoded, forKey: key)
        }
    }

    // MARK: – Intents
    func add(_ title: String) {
        guard !title.trimmingCharacters(in: .whitespaces).isEmpty else { return }
        items.append(TodoItem(title: title))
    }

    func delete(at offsets: IndexSet) { items.remove(atOffsets: offsets) }
    func move(from source: IndexSet, to destination: Int) { items.move(fromOffsets: source, toOffset: destination) }

    /// Toggle from TaskRow
    func toggle(_ item: TodoItem) {
        guard let idx = items.firstIndex(of: item) else { return }
        items[idx].isDone.toggle()
    }

    /// Update from DetailView
    func update(_ newItem: TodoItem) {
        guard let idx = items.firstIndex(of: newItem) else { return }
        items[idx] = newItem
    }
}
```

---

## 4  Refactor `TaskRow` (optional styling)

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

### Test checklist

1. **Add → Tap row → Edit title → Save** — list updates & persists.
2. Mark item done via **checkmark** or via **Toggle** inside detail view.
3. Swipe‑delete and drag‑reorder still work and remain after relaunch.

---

## 🌟 Bonus Ideas

* Add a *Cancel* button that discards `draft` changes.
* Show creation date in the detail view.
* Present detail view as a sheet instead of push navigation.
* Refactor persistence to **SwiftData** or **CoreData**.

---

### Up next

**Part 4:** SwiftData migration & search filtering.
