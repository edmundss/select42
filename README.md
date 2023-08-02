# Select42 Vue Component

Select42 is a Vue.js component that replicates the functionality of Select2 without the need for jQuery. It provides a customizable and user-friendly dropdown for selecting options. The component is built with Vue.js and does not have any external dependencies other than Vue itself.

## Installation
You can install the Select42 component using npm:
```
npm install select42
```

## Usage
To use the Select42 component, you can import it into your Vue project and register it as a local component:
```
<template>
  <div>
    <h2>Select42 Example</h2>
    <select42
      :options="options"
      :placeholder="placeholder"
      :theme="theme"
      :dropdownPosition="dropdownPosition"
      :ajaxUrl="ajaxUrl"
      :showSearch="showSearch"
      @option-selected="handleOptionSelected"
    ></select42>
  </div>
</template>

<script>
import Select42 from "select42";

export default {
  components: {
    Select42,
  },
  data() {
    return {
      options: [
        // Your options array here...
      ],
      placeholder: "Select an option",
      theme: "primary",
      dropdownPosition: "bottom",
      ajaxUrl: "", // Your AJAX endpoint URL here, if applicable
      showSearch: true,
    };
  },
  methods: {
    handleOptionSelected(option) {
      console.log("Selected option:", option);
    },
  },
};
</script>
```

##Props
| Property        | Type    | Default           | Description                                                                                                                                                            |
|-----------------|---------|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| options         | Array   | `[]`              | An array of objects representing the available options for the dropdown. Each object should have the properties `value` and `text`.                                  |
| placeholder     | String  | "Select an option"| The text to display when no option is selected.                                                                                                                      |
| theme           | String  | "primary"         | The CSS class name for customizing the appearance of the component.                                                                                                  |
| dropdownPosition| String  | "bottom"          | The position of the dropdown relative to the select box. Possible values are "bottom" (default) or "top".                                                            |
| ajaxUrl         | String  | `null`            | The URL to fetch options asynchronously when searching. If provided, the component will perform a search using this URL.                                              |
| showSearch      | Boolean | `true`            | A boolean indicating whether to display the search input within the dropdown. Default is `true`.                                                                    |


Events
@option-selected: This event is emitted when an option is selected. The selected option object is passed as the event payload.
Methods
toggleDropdown: Toggles the visibility of the dropdown.

Slots
The Select42 component also supports slots for customizing the content inside the dropdown.

default: Use this slot to customize the content of the dropdown options. Provide the content within <li> elements.
Styling
You can customize the appearance of the Select42 component by adding your own CSS classes or overriding the default classes provided in the component.

License
Select42 is released under the MIT License. Feel free to use and modify it according to your needs.
