<template>
  <div class="milestone-container">
    <div class="milestone" @scroll="loadPage">
      <div class="days"></div>

      <div class="days -head">
        <div
          class="day"
          v-for="(date, index) of dateRange"
          :key="index">
          <span>{{ format(date, 'ddd') }}</span>
          <span>{{ format(date, 'D') }}</span>
        </div>
      </div>

      <div
        class="days"
        v-for="(item, index) of formattedItems"
        :key="index">
        <div
          class="day -item"
          v-for="(date, dateIndex) of dateRange"
          :key="dateIndex"
          :style="colorize(index, date, item)"
          :class="[
            { '-enabled': isEnabled(date, item) },
            startOrEnd(date, item)
          ]">
          <span v-if="!compareAsc(date, item.dateStart)">{{ item.name }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import addDays from 'date-fns/add_days'
import eachDay from 'date-fns/each_day'
import format from 'date-fns/format'
import parse from 'date-fns/parse'
import compareAsc from 'date-fns/compare_asc'
import isInRange from 'date-fns/is_within_range'

export default {
  name: 'milestone',

  props: {
    items: Array,
    daysPerLoad: {
      type: Number,
      default: 2
    }
  },

  data () {
    return {
      pageStart: null
    }
  },

  computed: {
    formattedItems () {
      return JSON.parse(JSON.stringify(this.items)).map(item => {
        item.dateStart = parse(item.dateStart)
        item.dateEnd = parse(item.dateEnd)
        return item
      })
    },
    dateRange () {
      return eachDay(this.pageStart, addDays(this.pageStart, 13))
    }
  },

  mounted () {
    this.setStart()
  },

  methods: {
    setStart () {
      this.pageStart = new Date(
        JSON.parse(JSON.stringify(this.formattedItems))
          .sort((prev, next) => {
            return prev.dateStart > next.dateStart
          })[0].dateStart
      )
    },
    loadPage ({ target }) {
      const scrollWidth = target.scrollWidth
      const clientWidth = target.clientWidth
      const toLoad = this.daysPerLoad // days
      const toLoadWidth = scrollWidth / (this.dateRange.length / toLoad) // width of days toLoad
      let scrollLeft = target.scrollLeft

      if (scrollLeft + clientWidth === scrollWidth) {
        // Next page
        this.$emit('loadPage', 'Next')
        this.pageStart = addDays(this.pageStart, toLoad)
        target.scrollLeft = scrollWidth - toLoadWidth - clientWidth
      } else if (scrollLeft === 0) {
        // Previous page
        this.$emit('loadPage', 'Previous')
        this.pageStart = addDays(this.pageStart, -1 * toLoad)
        target.scrollLeft = toLoadWidth
      }
    },
    startOrEnd (date, item) {
      if (!compareAsc(date, item.dateStart)) {
        return '-start'
      } else if (!compareAsc(date, item.dateEnd)) {
        return '-end'
      }
    },
    colorize (index, date, item) {
      if (this.isEnabled(date, item)) {
        const hue = 255 * (index / this.formattedItems.length)
        return {
          backgroundImage: `linear-gradient(0deg, hsl(${hue}, 100%, 47%), hsl(${hue}, 100%, 50%))`
        }
      }
      return false
    },
    isEnabled: (date, item) => isInRange(date, item.dateStart, item.dateEnd),
    format,
    isInRange,
    compareAsc
  }
}
</script>

<style lang="scss" scoped>
.milestone {
  overflow-x: auto;
  padding-bottom: 1em;

  &-container {
    margin-bottom: 2em;
    max-width: 100%;
  }
}

.days {
  display: flex;
  flex-direction: row;
  margin-bottom: 0.5em;

  &.-head .day {
    border-bottom: 1px solid #cdcdcd;
  }
}

.day {
  flex: 0 0 22%;
  min-height: 1.5em;
  padding: 0.5em 1em;

  &.-enabled {
    background: #35495e;

    &.-start {
      border-radius: 6px 0 0 6px;
    }

    &.-end {
      border-radius: 0 6px 6px 0;
    }
  }

  &.-item {
    color: #fff;
    font-weight: 700;

    span {
      vertical-align: middle;
    }
  }

  @media screen and (min-width: 640px) {
    flex: 0 0 14%;
  }

  @media screen and (min-width: 960px) {
    flex: 0 0 12%;
  }

  @media screen and (min-width: 1280px) {
    flex: 0 0 8em;
  }
}
</style>
