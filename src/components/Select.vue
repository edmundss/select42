<template>
    <span
    tabindex="0"
    class="select42 select42-container"
    :class="[theme ? 'select42-' + theme : '', dropdownPosition ? 'select42-dropdown--' + dropdownPosition : '']"
    ref="select42"
    >
        <input
            type="hidden"
            :value="value"
        />
        <span 
            class="selection"
            @click="toggleDropdown"
        >
            <span 
                class="select42-selection select42-selection--single"
                :class="{'open': showDropdown}"
                tabindex="-1"
            >
                <span class="select42-selection__rendered">
                    <span v-show="value == null" class="select42-selection__placeholder">{{ placeholder }}</span>
                    {{ selectedOption?.text }}
                </span>
                <svg class="select42-selection__arrow" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                    <path d="M7 10l5 5 5-5z"/>
                    <path d="M0 0h24v24H0z" fill="none"/>
                </svg>
            </span>
        </span>
        <span
            class="dropdown-wrapper"
            v-show="showDropdown"
        >
            <span class="select42-dropdown select42-dropdown--below" dir="ltr">
                <span v-if="showSearch" class="select42-search select42-search--dropdown">
                    <input
                        @input="handleSearch"
                        @keydown="handleKeydown"
                        class="select42-search__field" 
                        type="search" 
                        tabindex="0" 
                        autocorrect="off" 
                        autocapitalize="none" 
                        spellcheck="false"
                        autocomplete="off" 
                        v-model="searchTerm"
                    />
                </span>
                <span class="select42-results">
                    <ul class="select42-results__options" role="listbox">
                        <li 
                            v-for="(option, index) in displayOptions" :key="index"
                            class="select42-results__option select42-results__option--selectable"
                            :class="{
                                'select42-results__option--highlighted': highlightIndex === index,
                                'select42-results__option--selected': selectedOption?.value === option.value,
                                'select42-results__option--disabled': option.disabled === 'true',
                            }"
                            @mouseover="highlightIndex = index"
                            @click="selectOption(option)"
                        >{{ option.text }}</li>
                    </ul>
                </span>
            </span>
        </span>
    </span>

</template>
  
<script>
import axios from "axios";

export default {
    name: "Select42",
    props: {
        options: {
            type: Array,
            default: [],
        },
        placeholder: {
            type: String,
            default: "Select an option",
        },
        theme: {
            type: String,
            default: "primary",
        },
        dropdownPosition: {
            type: String,
            default: "bottom",
        },
        ajaxUrl: {
            type: String,
            required: false,
        },
        showSearch: {
            type: Boolean,
            default: true,
        },
        modelValue: {
            type: [String, Number],
            default: "",
        },
    },
    emits: ["update:modelValue", "update:object"],
    data() {
        return {
            displayOptions: [],
            selectedOption: {},
            highlightIndex: 0,
            showDropdown: false,
            searchTerm: "",
        };
    },
    computed: {
        value: {
            get() {
                return this.modelValue
            },
            set(value) {
                this.$emit('update:modelValue', value)
                this.$emit('update:object', this.selectedOption)
            }
        }
    },
    // watch for changes in the modelValue
    watch: {
        modelValue: {
            immediate: true,
            handler(value) {
                // if modelValue is empty, set the selectedOption to null
                if (!value) {
                    this.selectedOption = null;
                } else {
                    // find the selected option
                    this.selectedOption = this.options.find(option => option.value == value);
                }
            }
        }
    },
    methods: {
        toggleDropdown() {
            this.showDropdown = !this.showDropdown;

            if(this.showDropdown) {
                window.addEventListener("click", this.handleOutsideClick);
                window.addEventListener("touchstart", this.handleOutsideClick);

                if (this.showSearch) {
                    this.$nextTick(() => {
                        this.$refs.select42.querySelector(".select42-search__field").focus();
                    });
                }

                if (this.ajaxUrl) {
                    this.fetchOptions();
                }
            } else {
                window.removeEventListener("click", this.handleOutsideClick);
                window.removeEventListener("touchstart", this.handleOutsideClick);
            }
        },
        selectOption(option) {
            this.selectedOption = option;
            this.toggleDropdown();
            this.value = option.value;
        },
        handleOutsideClick(event) {
            const select42 = this.$refs.select42;

            // If the clicked element is not the select42 element or its children, hide the dropdown
            if (!select42.contains(event.target)) {
                this.toggleDropdown();
            }
        },
        // method to sreach the options
        filterOptions() {
            this.displayOptions = this.options.filter(option => option.text.toLowerCase().includes(this.searchTerm.toLowerCase()));
        },
        fetchOptions() {
            axios.get(this.ajaxUrl, {
                params: {
                    q: this.searchTerm,
                }
            }).then(response => {
                this.displayOptions = response.data;
            });
        },
        handleKeydown(event) {
            const key = event.key;
            const optionsLength = this.displayOptions.length;

            if (key === "ArrowDown") {
                if (this.highlightIndex < optionsLength - 1) {
                    this.highlightIndex++;
                }
            } else if (key === "ArrowUp") {
                if (this.highlightIndex > 0) {
                    this.highlightIndex--;
                }
            } else if (key === "Enter") {
                this.selectOption(this.options[this.highlightIndex]);
            }
        },
        handleSearch() {
            
            if(this.ajaxUrl) {
                this.fetchOptions();
            } else {
                this.filterOptions();
            }
        },
    },
    mounted() {
        this.displayOptions = this.options;
        this.selectedOption = this.options.find(option => option.value == this.modelValue);
    },
  };
  </script>
  
<style>
    .select42-container .dropdown-wrapper {
        position: absolute;
        width: 100%;
        z-index: 99999;
    }

    .select42-container .select42-selection--single .select42-selection__arrow {
        fill: #888;
        height: 20px;
        float: right;
    }

    .select42-container .select42-selection--single .select42-selection__arrow {
        transition: transform 0.2s ease-in-out;
    }

    .select42-container .select42-selection--single.open .select42-selection__arrow {
        transform: rotate(180deg);
    }

    .select42-container {
        width: auto;
        display: block;
        padding: 1px;
        margin-bottom: 7px;
        box-sizing: border-box;
        margin: 0;
        position: relative;
    }

    .select42-container .select42-selection--single {
        box-sizing: border-box;
        cursor: pointer;
        display: block;
        -moz-user-select: none;
        -ms-user-select: none;
        user-select: none;
        -webkit-user-select: none;
        border: 0 !important;
        background-image: linear-gradient(to top, #9c27b0 2px, rgba(156, 39, 176, 0) 2px), linear-gradient(to top, #d2d2d2 1px, rgba(210, 210, 210, 0) 1px);
        /* background-size: 0 2px, 100% 1px; */
        background-repeat: no-repeat;
        background-position: center bottom, center calc(100% - 1px);
        background-color: transparent;
        transition: background 0s ease-out;
        float: none;
        box-shadow: none;
        border-radius: 0;

        /* background: no-repeat center bottom, center calc(100% - 1px); */
        background-size: 0 100%, 100% 100%;
        border: 0;
        height: 36px;
        transition: background 0s ease-out;
        padding-left: 0;
        padding-right: 0;
        border-radius: 0;
        font-size: 14px;

        display: block;
        width: 100%;
        padding: 0.4375rem 0;
        box-shadow: none;
        color: #495057;
    }

    .select42-container .select42-selection--single:focus,
    .select42-container .select42-selection--single.open {
        background-size: 100% 100%, 100% 100%;
        transition-duration: 0.3s;
        box-shadow: none;
    }

    .select42-dropdown {
        background: #fff;
        display: block;
        border-radius: 4px;
        border: none !important;
        transition: all 150ms linear;
        box-shadow: 0 20px 25px rgba(0, 0, 0, 0.15);
        padding: 5px 0;
        overflow: auto;
        width: 100%;
        top: 0;
    }

    .select42-search--dropdown {
        display: block;
        padding: 4px;
    }

    .select42-search__field {
        z-index: 10000000 !important;
        border: none !important;
        background-image: linear-gradient(#42a5f5, #42a5f5), linear-gradient(rgba(165, 181, 203, 0.5), rgba(165, 181, 203, 0.5)) !important;
        background-size: 0 2px, 100% 1px !important;
        background-repeat: no-repeat !important;
        background-position: center bottom, center calc(100% - 1px) !important;
        background-color: transparent !important;
        transition: background 0s ease-out !important;
        float: none !important;
        box-shadow: none !important;
        border-radius: 0 !important;
    }

    .select42-search--dropdown .select42-search__field {
        padding: 4px;
        width: 100%;
        box-sizing: border-box;
    }

    .select42-results {
        display: block;
    }

    .select42-results__options {
        list-style: none;
        margin: 0;
        padding: 0;
    }

    .select42-results>.select42-results__options {
        max-height: 200px;
        overflow-y: auto;
    }

    .select42-results__option {
        padding: 6px;
        -moz-user-select: none;
        -ms-user-select: none;
        user-select: none;
        -webkit-user-select: none;
    }

    .select42-results__option {
        list-style: none !important;
        cursor: pointer !important;
        word-wrap: break-word !important;
        font-size: 13px !important;
        padding: 10px 20px !important;
        margin: 0 5px !important;
        border-radius: 2px !important;
        transition: all 150ms linear !important;
    }

    .select42-results__option--selected {
        background-color: #ddd;
    }

    .select42-results__option.select42-results__option--highlighted {
        box-shadow: 0 14px 26px -12px rgba(236, 64, 122, 0.42), 0 4px 23px 0 rgba(0, 0, 0, 0.12), 0 8px 10px -5px rgba(236, 64, 122, 0.2) !important;
        color: #fff !important;
        background-color: #9c27b0 !important;
    }

    .select42-selection__placeholder {
        cursor: pointer;
        outline: none;
        color: #bac3ce !important;
        font-size: 14px !important;
        line-height: 1.42857 !important;
    }
</style>
  