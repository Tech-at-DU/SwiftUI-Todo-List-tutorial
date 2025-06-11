## SwiftUI To-Do List Tutorial

Welcome to the **SwiftUI To-Do List** tutorial, a step-by-step series that guides you from an empty Xcode project to a fully-featured task manager. You’ll start with a simple list of `String`s, then iterate toward a model-driven, MVVM-styled app that persists data with **UserDefaults** (and is ready for SwiftData later).

---

### What you’ll build

* Add new tasks with a text field and “+” button
* Swipe-to-delete and drag-to-reorder
* Checkmark to mark a task complete (strike-through text)
* Tap a row to open **TodoDetailView** and edit the title or status
* All tasks persist between launches using `UserDefaults`

> The finished source files (`TodoItem.swift`, `TodoViewModel.swift`, `ContentView.swift`, etc.) are included so you can peek at a working solution any time.

---

### Who is this tutorial for?

* **ACS 2420** students who have completed Lesson 2 (SwiftUI basics).
* Self-taught SwiftUI beginners who know `@State` and `List` but want to see **bindings**, **NavigationStack**, and **MVVM** in practice.
* Instructors looking for a concise, modern CRUD example that avoids Core Data boilerplate.

**Prerequisites**

| Tool  | Version               |
| ----- | --------------------- |
| macOS | 13 (Ventura) or newer |
| Xcode | 15 or newer           |
| Swift | 5.9+                  |

---

### What you’ll learn

| Topic                                                              | Covered in     |
| ------------------------------------------------------------------ | -------------- |
| **UserDefaults persistence** for custom types                      | Part 1         |
| `ObservableObject`, `@Published`, `@EnvironmentObject`             | Part 1         |
| `List` editing (`.onDelete`, `.onMove`)                            | Part 1         |
| Creating a simple **MVVM** view-model                              | Part 1         |
| Refactoring to a `TodoItem` struct with `id` & `isDone`            | Part 2         |
| Two-way bindings in `NavigationLink` detail views                  | Part 2         |
| Swipe-to-delete & drag-to-reorder on bound arrays                  | Part 2         |
| Editing tasks in **TodoDetailView** with a binding                 | Part 3         |
| Advanced UI polish, percent-encoding, haptic touch (stretch goals) | Part 3 / Bonus |

---

### Tutorial roadmap

| Part                             | Milestone                                                      |
| -------------------------------- | -------------------------------------------------------------- |
| **1. Starter app**               | Text-field input, add/delete tasks, persist array of `String`s |
| **2. Struct model & checkmarks** | `TodoItem`, check/uncheck, list reordering                     |
| **3. Detail screen**             | `NavigationStack`, editable `TodoDetailView`, two-way binding  |
| **4. (Optional)**                | Migrate to **SwiftData**, add search & widgets                 |

Each part ends with a working Xcode project and a checkpoint commit suggestion.

---

### How to use this repo

1. **Read the tutorial page** (Part 1, 2, and 3).
2. **Code along** in your own project; run frequently in the simulator.
3. **Compare** your progress to the `CompletedSource` folder if you get stuck.
4. Celebrate with ⌘-R when your tasks survive an app relaunch!
