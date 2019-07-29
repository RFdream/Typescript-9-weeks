<template>
  <div class="main__page">
      <div class="main__header">
          <div class="header">
              <div class="header__title"><img src="../assets/Group 1.svg" alt=""></div>
              <div class="header__split">|</div>
              <div class="header__subtitle">To-Do-List Alarm System</div>
              <div class="header__circle" v-show="!extend"><img src="../assets/Intersection 2.svg" alt="" width=220></div>
          </div>
          <div class="list" :class="{'list-cover': extend}" @click="listing('rollback')">
            <i class="fas fa-stream fa-2x"></i>
            <i class="fas fa-times fa-2x" v-if="extend"></i>
          </div>
      </div>
      <div class="main__content">
          <div class="circle__large left"></div>
          <div class="circle__large mid" :class="{extend: extend}">
            <div class="wrapper right">
                <div class="circle_animation circle-right" id="right-animation"></div>
            </div>
            <div class="wrapper left">
                <div class="circle_animation circle-left" :class="{'animate-left': working}"></div>
            </div>
            <div class="content">
                {{ doing }}
            </div>
            <div class="content time" v-if="work != '00:00'">{{ work }}</div>
            <div class="content time" v-if="work == '00:00'">{{ rest }}</div>
            <div class="action">
                <div class="circle__small">
                    <i class="fas fa-bell fa-lg"></i>
                </div>
                <div class="circle__small">
                    <div class="icon" @click="start" v-show="!working"><i class="fas fa-lg fa-play"></i></div>
                    <div class="icon" @click="timePause" v-show="working"><i class="fas fa-lg fa-pause"></i></div>
                </div> 
                <div class="circle__small" @click="end">
                    <i class="fas fa-step-forward fa-lg"></i>
                </div> 
            </div>
          </div>
          <div class="circle__dot" v-show="!extend"><img src="../assets/Intersection 1.svg" alt="" width=220></div>
          <div class="circle__large right" :class="{'circle__super': extend}">
              <div class="menu">
                  <div class="circle__medium" :class="{'active': selection === 'list'}" @click="listing('list')">
                      <i class="fas fa-list-ul fa-lg"></i>
                  </div>
                  <div class="circle__medium" :class="{'active': selection === 'chart'}" @click="listing('chart')">
                      <i class="fas fa-chart-line fa-lg"></i>
                  </div>
                  <div class="circle__medium" :class="{'active': selection === 'setting'}" @click="listing('setting')">
                      <i class="fas fa-cog fa-lg"></i>
                  </div>
              </div>
              <div class="content__list" v-show="selection === 'list'">
                <div class="input-bar">
                    <input
                        type="text"
                        class="add-mission"
                        placeholder="Add a new mission ..."
                        v-model="newMission"
                        @keyup.enter="addMission"
                    >
                    <i class="fas fa-plus fa-lg"/>
                </div>           
                <div class="listing">
                      <div class="listing__date">
                          <i class="fas fa-calendar-alt fa-lg"></i>
                          <div class="date-selection">
                            <i class="fas fa-caret-left fa-lg"></i>
                            {{ date }}
                            <i class="fas fa-caret-right fa-lg"></i>
                          </div>
                      </div>
                      <div class="listing__items">
                          <ul>
                              <li v-for="(item, index) in todos" :key="`item-${index}`">
                                  <label class="check-item">
                                    <input type="checkbox" class="check-circle" :checked="item.finish" v-model="item.finish">
                                    <span class="checkbox"></span>
                                  </label>
                                  <div class="item-content">{{ item.content }}</div>
                              </li>
                          </ul>
                      </div>
                </div>
              </div>
              <div class="content__chart" v-if="selection === 'chart'">
                <div class="statistics__text">
                    <div class="text today">
                        <span class="title">Today</span>
                        <br>
                        <span class="content">7</span>
                    </div>
                    <div class="text weekly">
                        <span class="title">Weekly</span>
                        <br>
                        <span class="content">60</span>
                    </div>
                    <div class="text totally" @click="draw">
                        <span class="title">Totally</span>
                        <br>
                        <span class="content">102</span>
                    </div>
                </div>
                <div class="time-selection">
                    <i class="fas fa-caret-left fa-lg"></i>
                    {{ date }}
                    <i class="fas fa-caret-right fa-lg"></i>
                </div>
                <div class="statistics__chart">
                    <canvas id="counting" width="800" height="450"></canvas>
                </div>
              </div>
              <div class="content__setting" v-if="selection === 'setting'">
                  <div class="setting-space">
                      <div class="label">Time Setting</div>
                      <div class="setting-box"><span>Working time</span>
                        <div class="time-setting">
                            <div class="dropdown-menu">
                                <li>{{ settingTimer / 60 }} min<i class="fas fa-angle-down fa-lg"></i></li>
                                <ul class="dropdown-content">
                                    <li v-for="time in timeSetting" :key="time" class="drop-time" @click="timer = settingTimer = time * 60">{{ time }}</li>
                                </ul>
                            </div>
                        </div>
                      </div>
                      <div class="setting-box">Resting time
                        <div class="time-setting">
                            <div class="dropdown-menu">
                                <li>{{ restingTimer / 60 }} min<i class="fas fa-angle-down fa-lg"></i></li>
                                <ul class="dropdown-content">
                                    <li v-for="time in restTimeSetting" :key="time" class="drop-time" @click="restingTimer = time * 60">{{ time }}</li>
                                </ul>
                            </div>
                        </div>
                      </div>
                  </div>
                  <div class="setting-space">
                      <div class="label">Alarm</div>
                      <div class="setting-box">Valume
                          <div class="time-setting toggle"><i class="fas fa-toggle-on fa-lg"></i></div>
                      </div>
                      <div class="setting-box">Audio
                        <div class="time-setting toggle-fixed">basic<i class="fas fa-angle-down fa-lg"></i></div>
                      </div>
                  </div>
              </div>
          </div>
          <div class="mission" :class="{ none: extend}">
              <input type="text" class="add-mission" placeholder="Add a new mission ..." v-model="newMission" @keyup.enter="addMission(newMission)">
              <i class="fas fa-plus"></i>
          </div>
      </div>
      <div class="main__footer" :class="{extend: extend}">
          Next: {{ nextTodo }}
      </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import { Component } from 'vue-property-decorator'
import moment from 'moment'
const Chart = require('chart.js')
interface TodoObject {
    id: number
    content: string
    finish: boolean
    time: string
}

@Component
export default class Clock extends Vue {
    newMission: string = ''
    timer: number = 1500
    settingTimer: number = 1500
    restingTimer: number = 300
    rest: string = '05:00'
    pause: boolean = false
    extend: boolean = false
    date: string = moment().format('MMMM,DD YYYY')
    selection: string = ''
    working: boolean = false
    timeSetting: number[] = [25, 20, 15]
    restTimeSetting: number[] = [5, 4, 3, 2, 1]
    todos: TodoObject[] = [
        {
            id: 1,
            content: 'Clean up the desk',
            finish: false,
            time: 'July,15 2019'
        },
        {
            id: 2,
            content: 'Feed the cat',
            finish: false,
            time: 'July,16 2019'
        }
    ]

    start(): void {
        if (this.pause) {
            this.pause = false
            document.getElementById('right-animation')!.style['animationPlayState'] = 'running'
            return
        }
        this.working = true
        document.getElementById('right-animation')!.classList.add('animate-right__1500')
        setTimeout(() => {
            if (this.timer <= 0) {
                this.timer = this.restingTimer
                return
            }
            this.timer -= 1
            this.start()
        }, 1000)
    }

    timePause(): void {
        this.pause = true
        this.working = false
        document.getElementById('right-animation')!.style['animationPlayState'] = 'pause'
    }
    listing(value: string): void {
        if (value === 'rollback') {
            if (this.extend) {
                this.extend = false
                this.selection = ''
                return
            }
            else {
                this.selection = 'list'
                this.extend = true
                return
            }
        }
        if (this.extend === false && this.selection !== value) {
            this.extend = true
            this.selection = value
        }
        else if (this.extend === true && this.selection !== value) this.selection = value
        else if (this.extend === true && this.selection === value) {
            this.selection = ''
            this.extend = false
        }
        if (value === 'chart' && this.extend === true) {
            this.selection = value
            this.$nextTick(() => {
                this.draw()
            })
        }
    }
    end(): void {
        if (this.status === 'work') {
            this.timer = 0
            this.working = false
            this.status = 'rest'
        }
    }

    addMission(): void {
        const todo: TodoObject = {
            id: this.todos.length + 1,
            content: this.newMission,
            finish: false,
            time: this.date
        }
        this.todos.push(todo)
        this.newMission = ''
    }

    public draw(): void {
        const ctx = document.getElementById('counting')
        new Chart(ctx, {
            type: 'line',
            data: {
                labels: ['7/19', '7/20', '7/21', '7/22', '7/23', '7/24', '7/25'],
                datasets: [{
                    defaultFontColor: 'rgba(242, 240, 201, 0.31)',
                    data: [8, 8, 7, 3, 9, 12, 9],
                    borderColor: '#ffffff',
                    backgroundColor: 'rgba(242, 240, 201, 0.31)',
                    fill: true
                }
                ]
            },
            options: {
                legend: {
                    display: false,
                    labels: {
                        // This more specific font property overrides the global property
                        fontColor: 'rgba(242, 240, 201, 0.31)'
                    }
                },
                scales: {
                    yAxes: [{
                        ticks: {
                            beginAtZero: true,
                            fontColor: 'rgba(242, 240, 201, 1)'
                        },
                    }],
                xAxes: [{
                        ticks: {
                            fontColor: 'rgba(242, 240, 201, 1)'
                        },
                    }]
                }
            }
        })
    }

    get work() {
        let sec = (this.timer % 60).toString()
        sec = sec.length === 2 ?  sec : '0' + sec
        let min = (Math.floor(this.timer / 60)).toString()
        min = min.length === 2 ? min : '0' + min
        return min + ':' + sec
    }

    get doing() {
        let todo: string = ''
        let temp: TodoObject[] = []
        temp = this.todos.filter((item: any) => {
            return item.finish === false
        })
        todo = temp.length >= 1 ? temp[0].content : 'Nothing Todo'
        return todo
    }

    get nextTodo() {
        let todo: string = ''
        let temp: TodoObject[] = []
        temp = this.todos.filter((item: any) => {
            return item.finish === false
        })
        todo = temp.length >= 2 ? temp[1].content : 'Nothing Next'
        return todo
    }

}
</script>


<style lang="sass">
    @import '../style/clock.sass'
</style>

