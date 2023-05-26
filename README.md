
# vuejs-3-datepicker-component

Here is a single Datepicker and date range picker file component for the VueJs-3. Single date picker can be possible or range selection can also be possible. Can set/view disabled dates.




## Demo

[Demo](https://codesandbox.io/s/vue-3-date-range-picker-tuc4sv)


## Installation

Simply put this component file anywhere inside VueJs-3 project as we make our custom component. It is too easy to use and integration and also can be modified by self.
    
## Features

- Date picker
- Date range picker
- Single calendar
- Two calendar
- Disabled dates
- Next month last month can be set
- First month as prev month ( LAST ) can be set


# Available Props

## :model-value 
Can pass the v-model variable to the component. \
`default:null` \
`type: Date, Object, String` 
## :is-range
If want a date picker then set it `false`. \
`default:true` \
`type: Boolean` 
## :enable-second-calendar
If want a single calendar then set to `false`. \
`default:true` \
`type: Boolean` 
## :given-date-format
Can pass the date format to tell the component that in which format you pass the date as selected/v-model value. \
`default:null` \
`type: String`
## :month-format
Can format the month that shown on calendar using this parameter. \
`default:MMM` [ Jan, Feb, Mar ] \
`type: String`
## :given-days
If there are a translations use and need days name customize then using this property can pass the custom days name. \
`default:null` [ Mon, Tue, Wed ] \
`type: Array`
## :calendar-class
Can change the parent calendar class using this parameter. \
`default:g-calendar` \
`type: String`
## :calendar-container-class
Can pass the custom class name for the calendar container for custom design. \
`default:calendar-container` \
`type: String`
## :calendar-footer-class
Can pass the custom class name for the calendar footer container for custom design. \
`default:calendar-footer` \
`type: String`
## :current-calendar-class
If there are two calendar or may be single calendar and need a custom style css class on that calendar then this parameter will be used. \
`default:current-calendar` \
`type: String`
## :next-calendar-class
If there are two calendar and need a custom style css class on that calendar then this parameter will be used. \
`default:next-calendar` \
`type: String`
## :btn-cancel-class
To pass the custom css class for the cancel button on the footer. \
`default:null` \
`type: String`
## :btn-clear-class
To pass the custom css class for the clear button on the footer. \
`default:null` \
`type: String`
## :btn-clear-text
Can set the custom text on the clear button. \
`default:null` \
`type: String`
## :btn-cancel-text
Can set the custom text on the cancel button. \
`default:null` \
`type: String`
## :next-prev-icon
If want to display the next/prev button to move on next and prev calendar . \
`default:false` \
`type: Boolean`
## :disabled-dates
Using this parameter one can display the disabled dates that not clickable for the users. One can pass the array of the dates to display the random dates. This parameter can accept Moment js object or any string date inside array.  \
`default:null` \
`type: Array`
## :disabled-from-to
Using this parameter one can set or display the disabled dates using a range. It will accept the object `{from: DATE, to: Date }`.  \
`default:null` \
`type: Object`
## :disabled-end-dates
If it is a range picker and one want to disabled the dates for the end date selection and not for the start date selection then one can pass the dates using this parameter. So, once user will select the start date then automatically disabled the dates when turn to select the end date.  \
`default:null` \
`type: Array`
## :disabled-end-from-to
If it is a range picker and one want to disabled the dates for the end date selection and not for the start date selection then one can pass the `from` and `to` dates binds with object using this parameter. So it will disable the dates within range.  \
`default:null` \
`type: Object`
## :is-today-highlight
Can highlight the today date on the calendar.  \
`default:true` \
`type: Boolean`
## :first-month
When date string present in this parameter then user only allowed to go back to given date month then prev button will be disabled and not able to go more previous on the calendar.  \
`default:null` \
`type: String`
## :start-month
On load the date picker which month want to display that decide by this property. \
`default:null` \
`type: Date, Object, Array, String`


## Events

One can capture the events on the component where this component is used.

### @onSelect
The selected dates array or object will be passed when date selection completed.

### @clearDates
To capture the events of clear button click and selection cleared.

### @onCancel
To capture the events of cancel button click and selection cleared.
## Further

You are always free to made any changes on the component as per your requirement as it is a single file component and you are just going to bind it in your project to use your own component where you can modify it as per your requirement.

