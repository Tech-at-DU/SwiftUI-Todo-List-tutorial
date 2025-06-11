# SwiftUI To‑Do List Tutorial – Part 4

### Stretch Goals & Next Steps

Congratulations on finishing Parts 1‑3! Your app now supports adding, editing, deleting, and persisting tasks. Below are optional challenges you can tackle to make the project production‑worthy or portfolio‑ready.

---

## 1  Persistence Upgrades

| Challenge                 | Hints                                                                        |
| ------------------------- | ---------------------------------------------------------------------------- |
| **SwiftData / Core Data** | Replace `UserDefaults` with a `@Model` macro. Use `TaskEntity` with `@Attribute` fields. Migrate data on first launch. |
| **iCloud sync**           | Use `NSUbiquitousKeyValueStore` for small data or enable CloudKit in SwiftData. Test on two simulators.                |
| **App Groups**            | Store the JSON file in an App Group container so a Home‑Screen widget or watchOS app can share the list.               |

---

## 2  UX & UI Polish

| Challenge             | Hints                                                                                |
| --------------------- | ------------------------------------------------------------------------------------ |
| **Search bar**        | Add `.searchable` to the `NavigationStack`; filter `vm.items` with `SearchableText`. |
| **Sections**          | Group tasks by completion: one section for “To‑Do”, one for “Done”.                  |
| **Drag handle**       | Show the ≡ drag‑handle icon only in `.editMode`.                                     |
| **Swipe accessories** | Add a “Done/Undo” swipe action with `.swipeActions(edge:.leading)`.                  |
| **Undo/Redo**         | Use `undoManager?.registerUndo(withTarget:)` inside `add`, `delete`, and `toggle`.   |
| **Haptics**           | Add `UIImpactFeedbackGenerator` in `toggle` and `delete`.                            |

---

## 3  Product Features

| Feature                   | Hints                                                                                                        |
| ------------------------- | ------------------------------------------------------------------------------ |
| **Due dates & Reminders** | Add a `Date? dueDate` to `TodoItem`. Use `DatePicker` in the detail view. Schedule a notification with `UNUserNotificationCenter`. |
| **Notifications summary** | Show an app badge with the number of overdue tasks.                            |
| **Widgets**               | Create a Home‑Screen widget that lists the top three tasks using WidgetKit.    |
| **Siri Shortcuts**        | Add `AppIntents` so users can say “Add todo buy milk”.                         |
| **Localization**          | Add `en`, `es`, and `fr` Localizable.strings.                                  |

---

## 4  Accessibility & Themability

| Challenge                     | Hints                                                                   |
| ----------------------------- | ----------------------------------------------------------------------- |
| **Dynamic Type**              | Replace hard‑coded font sizes with `.font(.body)` and `.scaledToFit()`. |
| **VoiceOver labels**          | Use `.accessibilityLabel` on custom buttons on the row.                 |
| **Dark‑mode friendly colors** | Move all colors into an asset catalog with Light/Dark variants.         |
| **High‑contrast mode**        | Test with Settings ▸ Accessibility ▸ Increase Contrast.                 |

---

## 5  Testing & CI

| Challenge          | Hints                                                                     |
| ------------------ | ------------------------------------------------------------------------- |
| **Unit tests**     | Write tests for `TodoViewModel` (`add`, `delete`, `toggle`, persistence). |
| **UI tests**       | Automate add‑edit‑delete flow with Xcode UI Testing.                      |
| **GitHub Actions** | Add `actions/checkout` + `apple-actions/setup-xcode` to build on push.    |

---

### How to proceed

1. Pick one stretch goal that excites you.
2. Create a new Git branch (`git checkout -b feature/search-bar`).
3. Commit early & often.
4. Push to GitHub and open a Pull Request for peer review.

These challenges will deepen your SwiftUI knowledge, strengthen your understanding of Apple’s frameworks, and give you great talking points in interviews. Good luck, and have fun extending your To‑Do app!
