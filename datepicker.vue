<template>
    <div :class="calendarClass">
      <div :class="calendarContainerClass">
        <div
          :class="data.classes"
          v-for="(data, dataIdx) in monthsData"
          :key="'month_data' + dataIdx"
        >
          <div class="calendar-header">
            <div class="month-name">
              <template v-if="dataIdx == 0">
                <span
                  @click="movePrevMonth()"
                  class="prev-icon"
                  v-if="nextPrevIcon"
                  :class="{ disabled: isPrevMonthDisabled }"
                >
                </span>
                <span @click="movePrevMonth()" class="prev-text" v-else>
                  PREV
                </span>
              </template>
  
              <span class="month-text"> {{ data.monthName }} </span>
              <template
                v-if="(!enableSecondCalendar && dataIdx == 0) || dataIdx == 1"
              >
                <span
                  @click="moveNextMonth()"
                  class="next-icon"
                  v-if="nextPrevIcon"
                  :class="{ disabled: isNextMonthDisabled }"
                >
                </span>
                <span @click="moveNextMonth()" class="next-text" v-else>
                  NEXT
                </span>
              </template>
            </div>
            <div class="day-name">
              <span v-for="(day, index) in daysName" :key="'date_name' + index">
                {{ day }}
              </span>
            </div>
          </div>
          <div class="calendar-dates">
            <template
              v-for="dateRowIdx in data.calendarRows"
              :key="'date_row_' + dataIdx + dateRowIdx"
            >
              <div class="date-row">
                <template
                  v-for="(dt, index) in data.dates.slice(
                    7 * (dateRowIdx - 1),
                    7 * dateRowIdx
                  )"
                >
                  <template v-if="typeof dt === 'object'">
                    <div
                      class="date"
                      :class="{
                        'date-highlighted': dt.highlighted,
                        'date-selected': dt.selected,
                        'date-disabled': dt.isDisabled,
                        'date-today': dt.isToday,
                        'date-selected-start': dt.startDateSelected,
                        'date-selected-end': dt.endDateSelected,
                      }"
                      :key="'calendar_dates' + dateRowIdx + index"
                      @click="onSelectDate(dt)"
                      @mouseover="hoverDate(dt)"
                    >
                      <span>
                        {{ dt.dateNumber }}
                      </span>
                    </div>
                  </template>
                  <div
                    v-else
                    :key="'blank_days' + dateRowIdx + index"
                    class="blank-day"
                  >
                    <!-- <span ></span> -->
                  </div>
                </template>
              </div>
            </template>
          </div>
        </div>
      </div>
      <div :class="calendarFooterClass">
        <div class="calendar-actions">
          <button
            class="btn btn-cancel"
            :class="btnCancelClass"
            @click="clickCancel"
          >
            {{ btnCancelText || "Cancel" }}
          </button>
          <button
            class="btn btn-clear"
            :class="btnClearClass"
            @click="clickClear"
          >
            {{ btnClearText || "Clear" }}
          </button>
        </div>
      </div>
    </div>
  </template>
    <script>
  import moment from "moment/moment";
  import "./default.scss";
  
  export default {
    props: {
      modelValue: {
        type: [Date, Object, String],
        default: null,
      },
      startMonth: {
        type: [Date, Object, Array, String],
        default: null,
      },
      isRange: {
        type: Boolean,
        default: true,
      },
      enableSecondCalendar: {
        type: Boolean,
        default: true,
      },
      givenDateFormat: {
        type: String,
        default: null,
      },
      monthFormat: {
        type: String,
        default: "MMM",
      },
      givenDays: {
        type: Array,
        default: null,
      },
      calendarClass: {
        type: String,
        default: "g-calendar",
      },
      calendarContainerClass: {
        type: String,
        default: "calendar-container",
      },
      calendarFooterClass: {
        type: String,
        default: "calendar-footer",
      },
      currentCalendarClass: {
        type: String,
        default: "current-calendar",
      },
      nextCalendarClass: {
        type: String,
        default: "next-calendar",
      },
      btnCancelClass: {
        type: String,
        default: null,
      },
      btnClearClass: {
        type: String,
        default: null,
      },
      btnCancelText: {
        type: String,
        default: null,
      },
      btnClearText: {
        type: String,
        default: null,
      },
      nextPrevIcon: {
        type: Boolean,
        default: false,
      },
      disabledDates: {
        type: Array,
        default: null,
      },
      disabledFromTo: {
        type: Object,
        default: () => {
          return null;
        },
      },
      disabledStartDates: {
        type: Array,
        default: null,
      },
      disabledStartFromTo: {
        type: Object,
        default: () => {
          return null;
        },
      },
      disabledEndDates: {
        type: Array,
        default: null,
      },
      disabledEndFromTo: {
        type: Object,
        default: () => {
          return null;
        },
      },
      isTodayHighlight: {
        type: Boolean,
        default: true,
      },
      lastMonth: {
        type: String,
        default: null,
      },
      firstMonth: {
        type: String,
        default: null,
      },
    },
    data() {
      return {
        start_date: new Date(),
        current_date: new Date(),
        selected_date: null,
        selected: {
          start_date: null,
          end_date: null,
        },
        start_date_selected: false,
      };
    },
    created() {
      this.init();
    },
    computed: {
      currentMonth() {
        return moment(this.current_date)
          .subtract(1, "M")
          .format(this.monthFormat || "MMMM");
      },
      nextMonth() {
        return moment(this.current_date).format(this.monthFormat || "MMMM");
      },
      daysName() {
        if (
          this.givenDays &&
          Array.isArray(this.givenDays) &&
          this.givenDays.length
        ) {
          return this.givenDays;
        }
        return ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"];
      },
      totalCalendarRows() {
        return Math.ceil(this.dates.length / 7);
      },
      nextCalendarRows() {
        return Math.ceil(this.nextMonthDates.length / 7);
      },
      blankDays() {
        const firstDay = moment(this.current_date)
          .subtract(1, "M")
          .startOf("month")
          .day();
        return firstDay - 1;
      },
      nextMonthBlankDays() {
        const firstDay = moment(this.current_date).startOf("M").day();
        return firstDay - 1;
      },
      nextMonthDates() {
        let startDate = moment(this.current_date).clone().startOf("month");
        let lastDate = startDate.clone().endOf("month");
        if (this.nextMonthBlankDays > 0) {
          return Array(this.nextMonthBlankDays).concat(
            this.getDates(startDate, lastDate)
          );
        } else {
          return this.getDates(startDate, lastDate);
        }
      },
      dates() {
        let startDate = moment(this.current_date)
          .subtract(1, "M")
          .startOf("month");
        let lastDate = moment(this.current_date).subtract(1, "M").endOf("month");
        if (this.blankDays > 0) {
          return Array(this.blankDays).concat(this.getDates(startDate, lastDate));
        } else {
          return this.getDates(startDate, lastDate);
        }
      },
      highlightedDates() {
        let dates = [];
        if (this.selected && this.selected.start_date && this.selected.end_date) {
          const startDate = this.selected.start_date.clone();
          const endDate = this.selected.end_date.clone();
          if (startDate.isBefore(endDate)) {
            let idxDate = startDate;
            while (idxDate.isSameOrBefore(endDate)) {
              dates.push(idxDate.clone().format("D-M-YYYY"));
              idxDate.add(1, "days");
            }
          } else {
            let idxDate = endDate.clone();
            while (idxDate.isSameOrBefore(startDate)) {
              dates.push(idxDate.clone().format("D-M-YYYY"));
              idxDate.add(1, "days");
            }
          }
          dates.shift();
          if (!this.start_date_selected && dates.length) {
            dates.pop();
          }
        }
        return dates;
      },
      monthsData() {
        let months = [
          {
            monthName: this.currentMonth,
            calendarRows: this.totalCalendarRows,
            dates: this.dates,
            classes: this.currentCalendarClass,
            isCurrentMonth: true,
          },
        ];
        if (this.enableSecondCalendar) {
          months.push({
            monthName: this.nextMonth,
            calendarRows: this.nextCalendarRows,
            dates: this.nextMonthDates,
            classes: this.nextCalendarClass,
            isNextMonth: true,
          });
        } else {
          months = [
            {
              monthName: this.nextMonth,
              calendarRows: this.nextCalendarRows,
              dates: this.nextMonthDates,
              classes: this.nextCalendarClass,
              isNextMonth: true,
            },
          ];
        }
        return months;
      },
      _disabledDates() {
        let disabledDates = [];
        if (
          this.disabledDates &&
          Array.isArray(this.disabledDates) &&
          this.disabledDates.length
        ) {
          disabledDates = this.transformDateIntoMoment(this.disabledDates);
        }
        if (
          this.isRange &&
          !this.start_date_selected &&
          this.disabledStartDates &&
          Array.isArray(this.disabledStartDates) &&
          this.disabledStartDates.length
        ) {
          disabledDates = disabledDates.concat(
            this.transformDateIntoMoment(this.disabledStartDates)
          );
        }
        if (
          this.isRange &&
          this.start_date_selected &&
          this.disabledEndDates &&
          Array.isArray(this.disabledEndDates) &&
          this.disabledEndDates.length
        ) {
          disabledDates = disabledDates.concat(
            this.transformDateIntoMoment(this.disabledEndDates)
          );
        }
        return disabledDates.map((d) => d.format("D-M-YYYY"));
      },
      isNextMonthDisabled() {
        if (this.lastMonth) {
          let currentDate = moment(this.current_date).clone();
  
          const lastMonth = this.transformDateIntoMoment(this.lastMonth);
          if (moment.isMoment(lastMonth)) {
            if (
              currentDate.isSameOrAfter(lastMonth, "month") &&
              currentDate.isSameOrAfter(lastMonth, "year")
            ) {
              return true;
            }
          }
        }
        return false;
      },
      isPrevMonthDisabled() {
        if (this.firstMonth) {
          let currentDate = moment(this.current_date).clone();
          if (this.enableSecondCalendar) {
            currentDate = currentDate.subtract(1, "M");
          }
          const firstMonth = this.transformDateIntoMoment(this.firstMonth);
          if (moment.isMoment(firstMonth)) {
            if (
              currentDate.isSameOrBefore(firstMonth, "month") &&
              currentDate.isSameOrBefore(firstMonth, "year")
            ) {
              return true;
            }
          }
        }
        return false;
      },
    },
    methods: {
      init() {
        if (this.isRange) {
          if (
            this.modelValue &&
            Array.isArray(this.modelValue) &&
            this.modelValue.length == 2
          ) {
            const startDate = this.modelValue[0];
            const endDate = this.modelValue[1];
            if (moment.isMoment(startDate)) {
              this.selected.start_date = startDate.startOf("day");
            } else {
              if (
                this.givenDateFormat &&
                this.givenDateFormat !== null &&
                this.givenDateFormat !== ""
              ) {
                this.selected.start_date = moment(startDate).startOf("day");
              } else {
                this.selected.start_date = moment(
                  startDate,
                  this.givenDateFormat
                ).startOf("day");
              }
            }
            if (moment.isMoment(endDate)) {
              this.selected.end_date = endDate.startOf("day");
            } else {
              if (
                this.givenDateFormat &&
                this.givenDateFormat !== null &&
                this.givenDateFormat !== ""
              ) {
                this.selected.end_date = moment(
                  endDate,
                  this.givenDateFormat
                ).startOf("day");
              } else {
                this.selected.end_date = moment(endDate).startOf("day");
              }
            }
          }
        } else {
          if (this.modelValue) {
            const selectedDate = this.modelValue;
            if (moment.isMoment(selectedDate)) {
              this.selected_date = selectedDate.startOf("day");
            } else {
              if (
                this.givenDateFormat &&
                this.givenDateFormat !== null &&
                this.givenDateFormat !== ""
              ) {
                this.selected_date = moment(
                  selectedDate,
                  this.givenDateFormat
                ).startOf("day");
              } else {
                this.selected_date = moment(selectedDate).startOf("day");
              }
            }
          }
        }
        if (this.startMonth && this.startMonth !== null) {
          if (moment.isMoment(this.startMonth)) {
            this.current_date = this.startMonth.toDate();
          } else {
            if (
              this.givenDateFormat &&
              this.givenDateFormat !== null &&
              this.givenDateFormat !== ""
            ) {
              this.current_date = moment(this.startMonth, this.givenDateFormat)
                .startOf("day")
                .toDate();
            } else {
              this.current_date = moment(this.startMonth).startOf("day").toDate();
            }
          }
        }
      },
      onSelectDate(dt) {
        if (dt.isDisabled) {
          return;
        }
        if (this.isRange) {
          if (!this.start_date_selected) {
            this.start_date_selected = true;
            this.selected.end_date = null;
            this.selected.start_date = dt.date.clone();
          } else {
            this.start_date_selected = false;
            if (this.selected.start_date.isAfter(dt.date)) {
              this.selected.end_date = this.selected.start_date;
              this.selected.start_date = dt.date;
            } else {
              this.selected.end_date = dt.date.clone();
            }
            this.emitRangeDate();
          }
        } else {
          this.selected_date = dt.date;
          this.emitRangeDate();
        }
      },
      hoverDate(dt) {
        if (this.start_date_selected) {
          // if( this.selected.start_date.isAfter( dt.date ) ){
  
          this.selected.end_date = dt.date.clone();
          // }else{
          //     this.selected.end_date = this.selected.start_date;
          //     this.selected.start_date = dt.date;
          // }
        }
      },
      isStartDate(dt) {
        if (
          this.isRange &&
          this.selected.start_date &&
          this.selected.start_date.isSame(dt)
        ) {
          return true;
        }
        return false;
      },
      isEndDate(dt) {
        if (
          this.isRange &&
          !this.start_date_selected &&
          this.selected.end_date &&
          this.selected.end_date.isSame(dt)
        ) {
          return true;
        }
        return false;
      },
      isSelectedDate(dt) {
        if (
          !this.isRange &&
          this.selected_date &&
          this.selected_date.isSame(dt)
        ) {
          return true;
        }
        return false;
      },
      isHighlightedDate(dt) {
        if (
          this.highlightedDates &&
          this.highlightedDates.length &&
          this.highlightedDates.includes(dt.format("D-M-YYYY"))
        ) {
          return true;
        }
        return false;
      },
      getDates(startDate, lastDate) {
        let dates = [];
        while (startDate.isBefore(lastDate)) {
          var isHighlighted = this.isHighlightedDate(startDate);
          var isToday =
            this.isTodayHighlight && moment().startOf("day").isSame(startDate);
          var isStartDate = this.isStartDate(startDate);
          var isEndDate = this.isEndDate(startDate);
          var isSelected =
            this.isSelectedDate(startDate) || isStartDate || isEndDate;
          var isDisabled = this.isDisabledDate(startDate);
          let tmpDt = {
            date: startDate.clone(),
            dateNumber: startDate.format("D"),
            highlighted: isHighlighted,
            selected: isSelected,
            toDate: startDate.toDate(),
            isDisabled: isDisabled,
            isToday: isToday,
            startDateSelected: isStartDate,
            endDateSelected: isEndDate,
          };
          dates.push(tmpDt);
          startDate = startDate.add(1, "day");
        }
        return dates;
      },
      moveNextMonth() {
        if (this.isNextMonthDisabled) {
          return;
        }
        this.current_date = moment(this.current_date).add(1, "M").toDate();
      },
      movePrevMonth() {
        if (this.isPrevMonthDisabled) {
          return;
        }
        this.current_date = moment(this.current_date).subtract(1, "M").toDate();
      },
      emitRangeDate() {
        let selected;
        if (this.isRange) {
          selected = [this.selected.start_date, this.selected.end_date];
        } else {
          selected = this.selected_date;
        }
        this.$emit("onSelect", selected);
        this.$emit("update:modelValue", selected);
      },
      clickClear() {
        if (this.isRange) {
          this.start_date_selected = false;
          this.selected.start_date = null;
          this.selected.end_date = null;
        } else {
          this.selected_date = null;
        }
        this.$emit("clearDates");
      },
      clickCancel() {
        this.$emit("onCancel");
      },
      transformDateIntoMoment(dates) {
        if (Array.isArray(dates)) {
          return dates.map((d) => {
            if (moment.isMoment(d)) {
              return d.startOf("day");
            } else {
              if (
                this.givenDateFormat &&
                this.givenDateFormat !== null &&
                this.givenDateFormat !== ""
              ) {
                return moment(d, this.givenDateFormat).startOf("day");
              } else {
                return moment(d).startOf("day");
              }
            }
          });
        } else {
          if (moment.isMoment(dates)) {
            return dates.startOf("day");
          } else {
            if (
              this.givenDateFormat &&
              this.givenDateFormat !== null &&
              this.givenDateFormat !== ""
            ) {
              return moment(dates, this.givenDateFormat).startOf("day");
            } else {
              return moment(dates).startOf("day");
            }
          }
        }
      },
      isInRangeFromTo(data, dt) {
        let fromDate = null;
        let toDate = null;
        if (data && typeof data == "object" && ("from" in data || "to" in data)) {
          if ("from" in data) {
            fromDate = this.transformDateIntoMoment(data.from);
          }
          if ("to" in data) {
            toDate = this.transformDateIntoMoment(data.to);
          }
        }
        if (fromDate && toDate) {
          if (dt.isSameOrAfter(fromDate) && dt.isSameOrBefore(toDate)) {
            return true;
          }
        } else {
          if (fromDate && dt.isSameOrAfter(fromDate)) {
            return true;
          }
          if (toDate && dt.isSameOrBefore(toDate)) {
            return true;
          }
        }
        return false;
      },
      isDisabledDate(dt) {
        if (
          this._disabledDates &&
          this._disabledDates.length &&
          this._disabledDates.includes(dt.format("D-M-YYYY"))
        ) {
          return true;
        }
        if (this.isInRangeFromTo(this.disabledFromTo, dt)) {
          return true;
        }
  
        if (this.isRange) {
          if (!this.start_date_selected) {
            if (this.isInRangeFromTo(this.disabledStartDates, dt)) {
              return true;
            }
          } else {
            if (this.isInRangeFromTo(this.disabledEndDates, dt)) {
              return true;
            }
          }
        }
        return false;
      },
    },
  };
  </script>
  