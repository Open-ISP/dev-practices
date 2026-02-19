# Github Label Conventions

We use a shared labelling schema across all Open-ISP repositories to keep things consistent and easy to navigate.

All labels follow a simple **`keyword: descriptor`** format, where the keyword tells you *what dimension* is being described, and the descriptor tells you the *specific value*. For example: `type: bug` or `category: git workflow`.

This makes the label list easy to scan, and makes it obvious what each label means at a glance.

---

## Label keywords

At the moment, we use two keywords to define all labels: `type` and `category`.

### `type` — what kind of issue or PR is this?

The `type` keyword describes the **nature** of an issue or PR. These labels are consistent across all Open-ISP repositories.

| Label                 | Description                                |
| --------------------- | ------------------------------------------ |
| `type: bug`           | Something isn't working                    |
| `type: documentation` | Improvements or additions to documentation |
| `type: feature`       | A new feature or request                   |
| `type: question`      | Further information is requested           |

An issue should always have exactly one `type` label assigned to it to keep things clear and simple. If an issue seems like it could span multiple types, consider whether it could be broken up into smaller more precise chunks, or choose the most appropriate `type` only.

### `category` — what area of the project does it relate to?

The `category` keyword describes the **subject matter** of an issue or PR. Unlike `type`, these labels are specific to each repository, since different repos have different focus areas.

For example, the `dev-practices` repo uses:

| Label                         | Description                       |
| ----------------------------- | --------------------------------- |
| `category: git workflow`      | Relates to git workflow practices |
| `category: naming convention` | Relates to naming conventions     |
| `category: style guide`       | Relates to the code style guide   |

Other repositories in the project will define their own `category` labels to suit their context. An issue can have more than one `category` label if needed, but again it's  worth considering whether the issue could be broken up to keep things concise.

### Using `type` and `category` together

These two keywords are independent of each other, and an issue should have both applied. A good way to think about it:

- **`type`** answers: *What is this?*
- **`category`** answers: *What is it about?*

For example, an issue labelled `type: bug` + `category: style guide` is a bug report about something in the style guide.

---

## Special labels

A small number of labels sit outside the `keyword: descriptor` format, where a simple flag makes more sense than a classified label.

| Label     | Description                                                                                                                                                                                |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `wontfix` | This issue has been considered and we've decided not to take further action. Issues closed with this label aren't forgotten — it's just a clear signal that a conscious decision was made. |

---

## Adding new labels

If you think a new label would be useful:

- Check whether an existing label already covers the need
- Follow the `keyword: descriptor` format, keeping the descriptor short and lowercase
- For new `category` labels, they can be added within the relevant repository as needed
- If you think a new *keyword* is needed (e.g. to cover a dimension not currently captured), raise it for discussion — we want to keep the schema simple and purposeful, so it's worth a quick conversation before adding

---

## Quick reference

> **Format:** `keyword: descriptor` — all lowercase
>
> **`type:`** keyword → the nature of the issue/PR; consistent across all repos; one per issue
>
> **`category:`** keyword → the subject area; varies per repo
>
> **`wontfix`** → a standalone flag for issues closed without further action
