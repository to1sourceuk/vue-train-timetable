<template lang="html">
  <div :class='"__vue-train-timetbale " + directionClass'>
    <ul :style='directionStyle' :class='"timetable " + directionClass' ref="timetable">
      <li v-for='stop, index in displayList' class='stop-item'>
        <i :class='"stop-bar " + appendBarClass(stop)' :style='barBackground'>
          <i class='start-point' :style='barBackground' v-if='is_start(stop)'></i>
          <i class='end-point' :style='barBackground' v-else-if='is_end(stop)'></i>
          <i class='stop-bar pointing' :style='barBackground' v-else></i>
        </i>
        <span :style='{color: text_color}' class='stop-text'>
          {{stop.name}}
          <i :class='stop.icon' v-if='stop.icon'></i>
        </span>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "vue-train-timetable",
  props: {
    timetable: {
      type: Array,
      default: [],
      required: true
    },
    position: {
      type: Number,
      default: 0,
      required: false
    },
    maximum_display: {
      type: Number,
      default: 10,
      required: false
    },
    minimum_display: {
      type: Number,
      default: 5,
      required: false
    },
    display_start: {
      type: Boolean,
      default: true,
      required: false
    },
    display_end: {
      type: Boolean,
      default: true,
      required: false
    },
    bar_color: {
      type: String,
      default: "rgb(235, 247, 149)",
      required: false
    },
    text_color: {
      type: String,
      default: "white",
      required: false
    },
    direction: {
      type: String,
      default: "vertical",
      required: false
    }
  },

  data() {
    return {};
  },

  watch: {},

  computed: {
    directionStyle() {
      let timetableEl = this.$refs.timetable;
      const result =
        this.direction == "vertical"
          ? {}
          : {
              width: this.displayList.length * 25 + "px"
            };
      return result;
    },
    directionClass() {
      return this.direction == "vertical" ? "vertical" : "horizontal";
    },
    barBackground() {
      return {
        "background-color": this.bar_color
      };
    },
    displayList() {
      let position = parseInt(this.position, 10);
      let size = this.timetable.length;
      let start = position;
      let end = position + this.maximum_display;
      if (end >= size) end = size;
      console.log(end - start, this.minimum_display);
      if (end - start < this.minimum_display) {
        start = end - this.minimum_display;
        if (start < 0) start = 0;
      }
      return this.timetable.slice(start, end);
    }
  },
  methods: {
    is_end(data) {
      return this.display_end ? this.timetable.indexOf(data) == this.timetable.length - 1 : false;
    },
    is_start(data) {
      return this.display_start ? this.timetable.indexOf(data) == 0 : false;
    },
    appendBarClass(data) {
      let list = this.displayList;
      let index = list.indexOf(data);
      if (index == list.length - 1) {
        return "end";
      } else if (index == 0) {
        return "start";
      }
      return "";
    }
  }
};
</script>

<style lang="less" scoped>
@point_size: 12px;
@bar_width: 15px;
@bar_text_padding: 20px;
@item_height: 25px;
.__vue-train-timetbale {
  font-family: helvetica, sans-serif;
  position: relative;
  font-weight: 500;
  box-sizing: border-box;
  ul {
    li {
      list-style: none;
    }
  }
  &.horizontal {
    // overflow-x: hidden;
  }
  .start-point,
  .end-point {
    width: @point_size;
    height: @point_size;
    border-radius: @point_size;
    position: absolute;
    left: 0;
    margin-left: -@point_size / 4;
  }
  .start-point {
    top: -@point_size / 2;
  }
  .end-point {
    bottom: -@point_size / 2;
  }
  .stop-item {
    padding: 5px 0 5px @bar_text_padding;
    box-sizing: border-box;
    position: relative;
    height: @item_height;
  }
  .stop-bar {
    position: absolute;
  }
  .timetable {
    &.horizontal {
      .start-point {
        top: -4px;
      }
      .stop-bar {
        width: 100%;
        height: 4px;
        top: 4px;
        left: 0;
        &.pointing {
          top: 0;
          width: 4px;
          height: 10px;
          position: absolute;
          left: 0;
        }
      }
      .stop-item {
        float: left;
        position: relative;
        width: @item_height;
        height: auto;
      }
      .stop-text {
        position: absolute;
        bottom: 0;
        left: 0;
        transform: rotateZ(90deg) translateX(2px);
        transform-origin: 0;
        height: 20px;
        width: 100%;
        min-width: 120px;
      }
    }

    &.vertical {
      .stop-bar {
        width: 5px;
        height: 100%;
        top: 0;
        left: 0;
        &.start {
          top: 50%;
          height: 50%;
          .pointing {
            top: 0;
          }
        }
        &.end {
          bottom: 50%;
          height: 50%;
          .pointing {
            top: 100%;
          }
        }
        &.pointing {
          height: 4px;
          width: @bar_width;
          top: 50%;
          left: 0;
          margin-top: -2px;
          // transform: translateX(50%);
        }
      }
    }
  }
}
.stop-text {
  font-size: 14px;
}
</style>
