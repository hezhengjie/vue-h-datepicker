<template>
    <div class="modal-wrapper" transition="modal" v-show="showStatus">
        <div class="modal-mask" @click="cancel"></div>
        <div class="modal-container">
            <div class="modal-btn-wrapper">
                <div class="btn" @click="cancel">取消</div>
                <div class="btn save" @click="saveDate">确认</div>
            </div>
            <div class="modal-content date">
                <div class="picker-date" @touchstart="touchstartYear" @touchmove="touchmoveYear" @touchend="touchendYear">
                    <ul class="year" :style="{transform: 'translate3d(0, ' + initOffsetYear + 'px, 0)'}">
                        <li></li>
                        <li></li>
                        <li v-for="year in years" v-text="year" track-by="$index"></li>
                        <li></li>
                        <li></li>
                    </ul>
                </div>
                <div class="picker-date" @touchstart="touchstart" @touchmove="touchmove" @touchend="touchend">
                    <ul class="month" :style="{transform: 'translate3d(0, ' + initOffsetMonth + 'px, 0)'}">
                        <li></li>
                        <li></li>
                        <li v-for="month in months" v-text="month" track-by="$index"></li>
                        <li></li>
                        <li></li>
                    </ul>
                </div>
                <div class="picker-date" @touchstart="touchstartDay" @touchmove="touchmoveDay" @touchend="touchendDay">
                    <ul class="day" :style="{transform: 'translate3d(0, ' + initOffsetDay + 'px, 0)'}">
                        <li></li>
                        <li></li>
                        <li v-for="day in days" v-text="day" track-by="$index"></li>
                        <li></li>
                        <li></li>
                    </ul>
                </div>
                <div class="up"></div>
                <div class="down"></div>
                <div class="line"></div>
            </div>
        </div>
    </div>
</template>

<style lang="less">
    html{
       font-size: 10px;
    }
    *{
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }
    ul,li,ol{
        list-style: none;
    }
    .modal-wrapper {
        width: 100%;
        height: 100%;
        position: fixed;
        top: 0;
        z-index: 21;
        transition: opacity .2s;
    }
    .modal-enter, .modal-leave {
        opacity: 0;
    }
    .modal-mask {
        width: 100%;
        height: 100%;
        position: absolute;
        top: 0;
        background-color: rgba(0, 0, 0, .4);
    }
    .modal-content {
        position: relative;
    }
    .modal-content.sex {
        width: 100%;
        position: absolute;
        bottom: 0;
        transition: all .3s;
        background-color: #F0F0F0;
    ul {
          padding: 0 .2rem;
          background-color: #FFF;
      }

    li {
          text-align: center;
          font-size: .32rem;
          line-height: .9rem;
      }

    li:first-child {
          border-bottom: 1px solid #EEE;
      }
    }
    .modal-enter .modal-content, .modal-leave .modal-content, .modal-enter .modal-container, .modal-leave .modal-container {
        transform: translateY(100%);
    }

    .modal-container {
        width: 100%;
        position: absolute;
        bottom: 0;
        height: 18rem;
        background-color: #FFF;
        transition: all .2s;
    }
    .modal-btn-wrapper {
        display: flex;
        justify-content: space-between;

    & .btn {
          width: 6rem;
          height: 3rem;
            line-height: 3rem;
        text-align: center;
        font-size: 1.4rem;
        color: #0088cc;

      }
    }

    .modal-content.date {
        margin: 0 4rem;
        font-size: 2rem;
        height: 15rem;
        text-align: center;
        & .picker-date {
            display: inline-block;
            width: 31%;
            height: 15rem;
            overflow: hidden;
        }

        & ul::-webkit-scrollbar {
            display: none;
        }

        & li {
            height: 3rem;
            text-align: center;
            font-size: 2rem;
            line-height: 3rem;
        }

        & .up, .down {
            width: 100%;
            height: 50%;
            position: absolute;
            pointer-events: none;
            background-image: linear-gradient(to bottom, #FFF, rgba(255, 255, 255, 0));
        }

        & .up {
            top: 0;
        }

        & .down {
            bottom: 0;
            background-image: linear-gradient(to top, #FFF, rgba(255, 255, 255, 0));
        }

        .line {
            width: 100%;
            height: 3rem;
            position: absolute;
            top: 50%;
            left: 50%;
            pointer-events: none;
            box-sizing: border-box;
            border-top: 1px solid #DCDCDC;
            border-bottom: 1px solid #DCDCDC;
            transform: translate(-50%, -50%);
        }
    }
</style>
<script>
    export default {
        props: ["status", "yearValue", "monthValue","dayValue","yearScope"],
        data() {
            return {
                months: [],
                years: [],
                days:[]

            }
        },
        computed: {
            showStatus(){
                return this.status;
            },
            fontSize(){
                let fontsize =getComputedStyle(window.document.documentElement)['font-size'].replace(/px/g,"");
                return fontsize
            },
            initOffsetMonth() {
                let value = - this.months.indexOf(this.monthValue) * this.fontSize*3;
                this.month && (this.month.offset = value);
                return value
            },
            initOffsetYear() {
                let value = - this.years.indexOf(this.yearValue)  * this.fontSize*3;
                this.year && (this.year.offset = value);
                return value
            },
            initOffsetDay() {
                let value = - this.days.indexOf(this.dayValue)  * this.fontSize*3;
                this.day && (this.day.offset = value);
                return value
            }

        },
        events: {
            touchstart(e, type) {
                this[type].startY = e.touches[0].clientY;
                this[type].speeds = [];
                this[type].time = Date.now()
            },
            touchmove(e, type) {

                let offset = e.touches[0].clientY - this[type].startY;
                let disance = this[type].offset + offset;
                if (Math.abs(offset) % (3*this.fontSize) === 0) {
                    let time = Date.now();
                    this[type].speeds.push((Math.abs(offset) / (time - this[type].time)).toFixed(2))
                }
                if (disance > 40) {
                    disance = 40;
                    this[type].limit = true;
                    return
                }
                if (disance < - this[type].maxOffset) {
                    disance = - this[type].maxOffset;
                    this[type].limit = true;
                    return
                }
                this[type].limit = false;
                this[type].node.style.transition = "none";
                this[type].node.style.transform = `translate3d(0, ${disance}px, 0)`
            },
            touchend(e, type) {
                let currentOffset = e.changedTouches[0].clientY - this[type].startY;
                let minScroll, maxScroll,
                        offset = this[type].offset,
                        fontSize = this.fontSize;
                this[type].speeds = this[type].speeds.map((v) => {
                            if (v !== "Infinity" || ! v) {
                    return v
                }
            })
                let scroll = false;
                let max = 0
                for (let i of this[type].speeds.slice(-2)) {
                    if (i * 1 > 1) {
                        scroll = true
                    }
                }
                if (scroll && ! this[type].limit) {
                    max = ~~ Math.max.apply(null, this[type].speeds)
                    maxScroll = Math.min(offset + (max * 10 * 3 * fontSize), 0)
                    minScroll = Math.max(offset - (max * 10 * 3 * fontSize), - (this[type].maxOffset - 40))
                    this[type].offset = currentOffset > 0 ? maxScroll : minScroll
                    let speed = Math.min.apply(null, this[type].speeds)
                    let speedTime = speed > 1 ? 0.2 : speed
                    if (type === "year") {
                        speedTime = speed > 2 ? speed / 2 : speed
                    }
                    this[type].node.style.transition = `all ${speedTime}s ease-out`;
                    this[type].node.style.transform = `translate3d(0, ${this[type].offset}px, 0)`;
                    return
                }
                this[type].node.style.transition = "all .15s ease-out";
                if (this[type].limit) {
                    if (currentOffset > 0) {
                        this[type].node.style.transform = `translate3d(0, 0, 0)`;
                        this[type].offset = 0
                    } else {
                        this[type].node.style.transform = `translate3d(0, -${this[type].maxOffset - 40}px, 0)`;
                        this[type].offset = - (this[type].maxOffset - 40)
                    }
                } else {
                    let perfect;
                    this[type].offset += currentOffset;
                    let next = 0;
                    let height = 3 * fontSize;
                    let index = ~~ (Math.abs(this[type].offset) / height);
                    let step = index * height;
                    let nextStep = (index + 1) * height;
                    if (nextStep - Math.abs(this[type].offset) < Math.abs(this[type].offset) - step && index !== 11 && index !== 0) {
                        next = 1
                    }
                    perfect = next ? nextStep : step;
                    this[type].offset = - perfect;
                    this[type].node.style.transform = `translate3d(0, -${perfect}px, 0)`
                }
            }
        },
        methods: {
            cancel(){
                this.$dispatch('cancelPicker');
            },
            saveDate() {
                let step = ~~ (3 * this.fontSize),
                        yearTop =  Math.abs(this.year.offset),
                        monthTop =  Math.abs(this.month.offset),
                dayTop =  Math.abs(this.day.offset);
                let yearValue = this.years[Math.min(99, ~~ (yearTop / step))];
                let monthValue = this.months[~~ (monthTop / step)];
                let dayValue = this.days[~~ (dayTop / step)];
                this.$dispatch('getDate',{year:yearValue,
                    month:monthValue,
                    day:dayValue});
                this.$dispatch('cancelPicker');
            },
            touchstart(e) {
                this.$emit("touchstart", e, "month")
            },
            touchmove(e) {
                this.$emit("touchmove", e, "month")
            },
            touchend(e) {
                this.$emit("touchend", e, "month")
            },
            touchstartYear(e) {

                this.$emit("touchstart", e, "year")
            },
            touchmoveYear(e) {
                this.$emit("touchmove", e, "year")
            },
            touchendYear(e) {
                this.$emit("touchend", e, "year")
            },
            touchstartDay(e) {
                this.$emit("touchstart", e, "day")
            },
            touchmoveDay(e) {
                this.$emit("touchmove", e, "day")
            },
            touchendDay(e) {
                this.$emit("touchend", e, "day")
            }
        },
        ready() {
            let year = 0, month = 0,day=0,
                    fontSize = this.fontSize,
                    yearLimit = 40,
                    currentYear = new Date().getFullYear()+Math.floor(yearLimit/2);
            if(this.yearScope){
                yearLimit = this.yearScope[1]-this.yearScope[0];
                currentYear = this.yearScope[1];
            }
            while (year <= yearLimit) {
                this.years.unshift(currentYear - year);
                year++
            }
            while (month < 12) {
                this.months.push(month + 1);
                month++
            }
            while (day < 31) {
                this.days.push(day + 1);
                day++
            }
            this.month = {
                node: document.querySelector(".month"),
                offset: 0,
                maxOffset: ~~ (12* 3 * fontSize)
            };
            this.year = {
                node: document.querySelector(".year"),
                offset: 0,
                maxOffset: ~~ (yearLimit* 3* fontSize)
            };
            this.day = {
                node: document.querySelector(".day"),
                offset: 0,
                maxOffset: ~~ (31* 3 * fontSize)
            }
        }
    }
</script>
