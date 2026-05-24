# Select42 Vue Component

Select42 is a lightweight Vue.js select component inspired by Select2 — but built entirely without jQuery. It provides a clean, searchable, and customizable dropdown experience for Vue applications while keeping setup simple and dependency-free.

Designed originally for internal enterprise applications, Select42 supports:

- Searchable dropdowns
- AJAX-powered async loading
- `v-model` support
- Custom option objects
- Keyboard navigation
- Clearable selections
- Vue event emission
- Bootstrap-friendly styling

Unlike Select2, Select42 is built specifically for modern Vue projects and does not require jQuery or additional UI frameworks.

> DISCLAIMER: This is my first NPM package and was originally developed for my own projects and workflows. It may not cover every edge case or use case. Use at your own risk. Community contributions are welcome.

---

# Installation

Install via npm:

```bash
npm install @sulzanoks/select42
```

---

# Usage

```vue
<template>
    <div>
        <h2>Select42 Example</h2>

        <Select42
            ref="mySelect42Ref"
            v-model="selectedValue"
            :options="options"
            :placeholder="placeholder"
            :ajaxUrl="ajaxUrl"
            :showSearch="showSearch"
            @change="handleOptionSelected"
        />
    </div>
</template>

<script>
import Select42 from "@sulzanoks/select42";
import "@sulzanoks/select42/dist/style.css";

export default {
    components: {
        Select42,
    },

    data() {
        return {
            selectedValue: null,

            options: [
                { value: "1", text: "One" },
                { value: "2", text: "Two" },
                { value: "3", text: "Three" },
            ],

            placeholder: "Select an option",

            // Optional AJAX endpoint for async searching
            ajaxUrl: "",

            showSearch: true,
        };
    },

    methods: {
        handleOptionSelected(value) {
            console.log("Selected value:", value);
        },
    },
};
</script>
```

---

# Props

| Property | Type | Default | Description |
|---|---|---|---|
| `options` | `Array` | `[]` | Array of option objects. Each object should contain `value` and `text` properties. |
| `placeholder` | `String` | `"Select an option"` | Placeholder text displayed when nothing is selected. |
| `theme` | `String` | `"primary"` | Optional theme name used for CSS styling. |
| `dropdownPosition` | `String` | `"bottom"` | Dropdown placement (`bottom` or `top`). |
| `ajaxUrl` | `String` | `null` | AJAX endpoint used for async searching/loading options. |
| `showSearch` | `Boolean` | `true` | Enables or disables the search input inside the dropdown. |
| `modelValue` | `String \| Number` | `null` | Bound selected value used with `v-model`. |

---

# Option Format

Basic format:

```js
[
    {
        value: 1,
        text: "Option Label"
    }
]
```

You may also include additional custom fields:

```js
[
    {
        value: 1,
        text: "John Doe",
        email: "john@example.com",
        department: "IT"
    }
]
```

The full selected object will still be available through events and refs.

---

# Events

| Event | Description |
|---|---|
| `@update:modelValue` | Emitted when selected value changes. |
| `@update:object` | Emits the full selected option object. |
| `@change` | Emitted whenever the value changes. |
| `@select` | Emitted only when an option is selected. |
| `@clear` | Emitted when selection is cleared. |

---

# Accessing Selected Data

You can access the current value or full selected object through component refs.

### Selected value

```js
this.$refs.mySelect42Ref.value
```

### Selected option object

```js
this.$refs.mySelect42Ref.selectedOption
```

Example:

```js
console.log(this.$refs.mySelect42Ref.selectedOption.email);
```

---

# AJAX Loading

When `ajaxUrl` is provided, Select42 will automatically perform async searches while typing.

Example endpoint response:

```json
[
    {
        "value": 1,
        "text": "John Doe"
    },
    {
        "value": 2,
        "text": "Jane Smith"
    }
]
```

The request will include:

```http
GET /your-endpoint?q=searchTerm
```

---

# Methods

| Method | Description |
|---|---|
| `toggleDropdown()` | Toggles dropdown visibility. |
| `closeDropdown()` | Closes the dropdown. |
| `clearSelection()` | Clears the current selection. |

---

# Styling

Select42 ships with its own stylesheet:

```js
import "@sulzanoks/select42/dist/style.css";
```

The component is designed to work well alongside Bootstrap 5, but Bootstrap itself is not required.

---

# Features

- No jQuery dependency
- Lightweight and simple
- Vue-native implementation
- Search support
- AJAX support
- Keyboard navigation
- Clearable selections
- Bootstrap-friendly UI
- Custom option object support

---

# License

Select42 is released under the MIT License.