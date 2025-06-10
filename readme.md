# SwiftUI Todo list tutorial

# SwiftUI To‑Do List Tutorial – Part 1

> **Goal**  Build a simple To‑Do app in SwiftUI that
>
> 1. lets the user add & delete tasks and
> 2. stores those tasks in **UserDefaults** so they persist across launches.
>
> This series assumes you already know basic Swift and the SwiftUI syntax covered in ACS 2420 Lesson 2.

---

## 🧰 Prerequisites

* **Xcode 15** or newer (download from the Mac App Store)
* macOS 13+ (Ventura) or macOS 14 (Sonoma)
* Completed Lesson 2 (Stacks, `@State`, `List`, `NavigationStack`).

---

## 1  Create a new SwiftUI project

1. **File ▸ New ▸ Project…**
2. Choose **iOS ▸ App** → **Next**.
3. **Product Name** `TodoListSwiftUI`
4. **Interface** *SwiftUI*   **Language** *Swift*
5. Disable *Tests* (for now). → **Next**, choose a save location and **Create**.

Xcode generates three files:

* `TodoListSwiftUIApp.swift` (the app entry point)
* `ContentView.swift` (the default view)
* `Assets.xcassets` & `Info.plist` (resources/metadata)

Run the project (⌘‑R). You should see the “Hello, world!” template.

---

## 2  Plan the data model

We’ll store an array of **`String`** tasks in UserDefaults. Later parts will upgrade to a struct with an `id` and `isDone` flag, but strings keep Part 1 laser‑focused.

```swift
/// Key used in UserDefaults
private let kTasksKey = "tasks"

/// Helpers for saving & loading
extension UserDefaults {
    var savedTasks: [String] {
        get { stringArray(forKey: kTasksKey) ?? [] }
        set { set(newValue, forKey: kTasksKey) }
    }
}
```

---

## 3  Create `TodoViewModel`

`ObservableObject` acts as the single source of truth.

```swift
final class TodoViewModel: ObservableObject {
    @Published var tasks: [String] {
        didSet { UserDefaults.standard.savedTasks = tasks }
    }

    // MARK: – Init
    init() {
        tasks = UserDefaults.standard.savedTasks
    }

    // MARK: – Intent(s)
    func add(_ title: String) {
        guard !title.trimmingCharacters(in: .whitespaces).isEmpty else { return }
        tasks.append(title)
    }

    func delete(at offsets: IndexSet) {
        tasks.remove(atOffsets: offsets)
    }
}
```

> **Why UserDefaults?** Small, key‑value storage perfect for fewer than \~50 simple items. For larger data you’d use SwiftData or Core Data.

---

## 4  Build the UI (replace `ContentView`)

```swift
struct ContentView: View {
    @State private var newTask = ""
    @EnvironmentObject var vm: TodoViewModel

    var body: some View {
        NavigationStack {
            VStack(spacing: 12) {
                HStack {
                    TextField("Add a task…", text: $newTask)
                        .textFieldStyle(.roundedBorder)
                        .submitLabel(.done)
                        .onSubmit(addTask)
                    Button(action: addTask) {
                        Image(systemName: "plus.circle.fill")
                            .font(.title2)
                    }
                }
                .padding(.horizontal)

                List {
                    ForEach(vm.tasks, id: \ .self) { task in
                        Text(task)
                    }
                    .onDelete(perform: vm.delete)
                }
            }
            .navigationTitle("My Tasks")
        }
    }

    private func addTask() {
        vm.add(newTask)
        newTask = ""
    }
}
```

### Wire the environment object in `TodoListSwiftUIApp.swift`

```swift
@main
struct TodoListSwiftUIApp: App {
    @StateObject private var vm = TodoViewModel()

    var body: some Scene {
        WindowGroup {
            ContentView()
                .environmentObject(vm)
        }
    }
}
```

Run again (⌘‑R). You can now:

* Type a task → tap **+** (or press *Return*) to add.
* Swipe‑delete rows.
* Relaunch the app—tasks persist! ✅

---

## 🔍 What’s next?

**Part 2** will:

1. Refactor `tasks` into a struct (`TodoItem`) with `id` & `isDone`.
2. Toggle completion with a checkmark.
3. Add edit‑mode reordering.
4. Migrate persistence from UserDefaults to **SwiftData**.

Save & commit your work (`git init`, `git add .`, `git commit -m "Part1"`).
