<template>
  <div class="main__page">
      <div class="main__header">
          <div class="header">
              <div class="header__title"><img src="../assets/Group 1.svg" alt=""></div>
              <div class="header__split">|</div>
              <div class="header__subtitle">To-Do-List Alarm System</div>
              <div class="header__circle"><img src="../assets/Intersection 2.svg" alt="" width=220></div>
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
                <div class="circle_animation circle-right" :class="{'animate-right': working}"></div>
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
                <div class="circle__small" :class="{working: working}" @click="start">
                    <i class="fas fa-lg fa-play"></i>
                    <i class="fas fa-lg fa-pause" v-if="working"></i>
                </div> 
                <div class="circle__small" @click="end">
                    <i class="fas fa-step-forward fa-lg"></i>
                </div> 
            </div>
          </div>
          <div class="circle__dot"><img src="../assets/Intersection 1.svg" alt="" width=220></div>
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
              <div class="content__list" v-if="extend && selection === 'list'">
                  <div class="input-bar">
                      <input type="text" class="add-mission" placeholder="Add a new mission ...">
                      <i class="fas fa-plus fa-lg"></i>        
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
                              <li v-for="item in todos" :key="item">
                                  <label class="check-item">
                                    <input type="checkbox" class="check-circle" :checked="item.finish">
                                    <span class="checkbox"></span>
                                  </label>
                                  <div class="item-content">{{ item.content }}</div>
                              </li>
                          </ul>
                      </div>
                  </div>
              </div>
              <div class="content__chart" v-if="extend && selection === 'chart'">
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
              <div class="content__setting" v-if="extend">
                  <div class="setting-space">
                      <div class="label">Time Setting</div>
                      <div class="setting-box"><span>Working time</span>
                        <div class="time-setting">25min<i class="fas fa-angle-down fa-lg"></i></div>
                      </div>
                      <div class="setting-box">Resting time
                        <div class="time-setting fixed-setting">5min<i class="fas fa-angle-down fa-lg"></i></div>
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
          <div class="mission" :class="{ none: extend}" v-if="selection === 'setting'">
              <span>Add a new mission...</span>
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
type todoObject = {
    id: number
    content: string
    finish: boolean
    time: string
}

@Component
export default class Clock extends Vue{
    timer: number = 1500
    work: string = "25:00"
    rest: string = "05:00"
    status: string = "work"
    pause: boolean = false
    extend: boolean = false
    date: string = moment().format('MMMM,DD YYYY')
    selection: string = ''
    working: boolean = false
    todos: Array<todoObject> = [
        {
            'id': 1,
            'content': 'Clean up the desk',
            'finish': false,
            'time': 'July,15 2019'
        },
        {
            'id': 2,
            'content': 'Feed the cat',
            'finish': false,
            'time': 'July,16 2019'
        }
    ]

    start(): void {
        this.working = true
        setTimeout(() => {
            if (this.timer <= 0) {
                this.timer = 300
                return
            }
            this.timer -= 1
            let sec = (this.timer % 60).toString()
            sec = sec.length == 2 ?  sec : '0'+ sec
            let min = (Math.floor(this.timer / 60)).toString()
            min = min.length == 2 ? min : '0'+ min
            this.work = min + ':' + sec
            this.start()
        }, 1000)
    }
    listing(value: string): void {
        if (value === 'rollback') {
            if (this.extend) {
                this.extend = !this.extend
                this.selection = ''
                return
            }
            else{
                this.selection = 'list'
                this.extend = true
                return
            }
        }
        if (this.extend === false && this.selection !== value) {
            this.extend = !this.extend
            this.selection = value
        }
        else if (this.extend === true && this.selection !== value) {
            this.selection = value
        }
        else if (this.extend === true && this.selection === value) {
            this.selection = ''
            this.extend = !this.extend
        }
        if (value === 'chart' && this.extend === true) {
            this.$nextTick(()=>{
                this.draw()
            })
        }
    }
    end(): void {
        if(this.status === 'work') {
            this.timer = 0
            this.work = '00:00'
            this.working = false
            this.status = 'rest'
        }
    }

    draw(): void {
        let ctx = document.getElementById('counting')
        new Chart(ctx, {
            type: 'line',
            data: {
                labels: ['7/19', '7/20', '7/21', '7/22', '7/23', '7/24', '7/25'],
                datasets: [{ 
                    defaultFontColor: 'rgba(242, 240, 201, 0.31)',
                    data: [8, 8, 7, 3, 9, 12, 9],
                    borderColor: "#ffffff",
                    backgroundColor: "rgba(242, 240, 201, 0.31)",
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
                            beginAtZero:true,
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
        });
    }

    mounted() {
        this.draw()
    }
    get doing(){
        let todo: string = ''
        let temp: Array<todoObject> = []
        temp = this.todos.filter((item: any) => {
            return item.finish === false
        })
        todo = temp[0].content
        
        return todo
    }

    get nextTodo() {
        let todo: string = ''
        let temp: Array<todoObject> = []
        temp = this.todos.filter((item: any) => {
            return item.finish === false
        })
        todo = temp[1].content
        
        return todo
    }
}
</script>


<style lang="sass">
    body
      background-color: #BC2B35
      font-family: Century Gothic
      margin: 0
      padding: 0
      opacity: 1
      overflow: hidden
    .main
        &__header
            height: 41px
            color: #F2F0C9
            margin-top: 35px
            display: flex
            

            .header
                margin-left: 51px
                display: flex

                &__title
                    line-height: 41px
                    font-size: 41px
                &__split
                    line-height: 41px
                    font-size: 35px
                    margin-left: 30px
                &__subtitle
                    line-height: 41px
                    font-size: 16px
                    margin-left: 20px
                    opacity: 0.6

                &__circle
                    margin-top: -40px
                    margin-left: 15px

            .list
                line-height: 41px
                position: absolute
                right: 68px
                cursor: pointer
                z-index: 10

                .fa-times
                    display: none

            .list-cover
                .fa-stream
                    display: none

                .fa-times
                    display: initial

            

        &__content
            height: 380px
            margin-top: 80px

            .circle__large
                height: 420px
                width: 420px
                border-radius: 420px
                background-color: #D9343F
                position: absolute
                z-index: 1
                opacity: 0.7

            .circle__dot
                position: absolute
                top: 86%
                left: 70%

            .left
                left: -190px
            
            .right
                right: -190px

                .menu
                    width: 85px
                    position: absolute
                    left: 110px
                    top: 50%
                    transform: translate(0, -50%)
                    
                    .circle__medium
                        width: 60px
                        height: 60px
                        border-radius: 60px
                        cursor: pointer
                        border: 1px solid #F2F0C9
                        text-align: center
                        margin-top: 15px
                        color: #F2F0C9
                        line-height: 65px
                        z-index: 5
                        background-color: #BC2B35

                    .active
                        background-color: #D9343F

            .circle__super
                height: 950px
                width: 950px
                border-radius: 950px
                right: -200px
                top: -100px

                .menu
                    left: -30px

                .content__list
                    width: 480px
                    height: 410px
                    z-index: 5
                    margin-top: 260px
                    margin-left: 200px

                    .input-bar
                        width: 480px
                        height: 52px
                        
                        .add-mission
                            outline: none
                            width: 100%
                            height: 35px
                            border-radius: 5px
                            background-color: #f9f8e6
                            border: none
                            font-size: 24px
                            line-height: 35px
                            text-indent: 30px
                            
                        
                        ::placeholder
                            color: #BC2B35
                            opacity: 0.55

                        .svg-inline--fa
                            position: absolute
                            right: 285px
                            top: 268px
                            color: #BC2B35
                            line-height: 35px
                            z-index: 100
                            cursor: pointer

                    .listing
                        width: 100%
                        margin-top: 20px
                        height: 400px
                        border-radius: 10px
                        background-color: #BC2B35

                        &__date
                            display: flex
                            background-color: #E9414C
                            color: #F2F0C9
                            width: 100%
                            height: 40px
                            border-radius: 5px
                            line-height: 40px

                            .date-selection
                                margin-left: 60px
                                font-size: 20px
                                margin-top: -3px

                            .svg-inline--fa
                                margin-left: 25px
                                margin-top: 10px

                            .fa-calendar-alt
                                margin-right: 30px

                            .fa-caret-left
                                margin-right: 15px
                                cursor: pointer
                            .fa-caret-right
                                margin-left: 15px
                                cursor: pointer

                        &__items
                            margin-top: 35px
                            margin-left: 5%
                            width: 90%
                            height: 295px

                            .check-item
                                display: block
                                position: relative
                                margin-bottom: 12px
                                cursor: pointer
                                font-size: 22px
                                -webkit-user-select: none
                                -moz-user-select: none
                                -ms-user-select: none
                                user-select: none

                            .check-circle
                                opacity: 0
                                cursor: pointer
                                position: absolute

                            .checkbox
                                background: transparent
                                border: 3px solid #F2F0C9
                                border-radius: 18px
                                width: 18px
                                height: 18px
                                display: block
                                position: relative
                                cursor: pointer

                            .checkbox:after
                                content: ""
                                position: absolute
                                display: none
                                border: solid #F2F0C9
                                border-width: 0 3px 3px 0
                                -webkit-transform: rotate(45deg)
                                -ms-transform: rotate(45deg)
                                transform: rotate(45deg)
                                width: 10px
                                height: 15px
                                margin-top: -3px
                                margin-left: 5px

                            input:checked ~.checkbox:after 
                                display: block

                            .item-content
                                width: 85%
                                height: 40px
                                margin-left: 15px
                                margin-top: -10px
                                border-bottom: 1px dashed #F2F0C9
                                color: #F2F0C9
                                opacity: 0.9
                                font-size: 20px
                                line-height: 40px
                                margin-bottom: 15px

                            ul,li
                                list-style: none
                                padding: 0
                                margin: 0
                            
                            li
                                display: flex

                .content__chart
                    width: 430px
                    height: 410px
                    z-index: 5
                    margin-top: 260px
                    margin-left: 200px

                    .statistics__text
                        width: 100%
                        height: 90px
                        display: flex

                        .text
                            width: 25%
                            height: 100%
                            font-size: 28px
                            color: #F2F0C9
                            text-align: center
                            display: block
                            line-height: 45px

                        .title
                            font-size: 22px
                        
                        .content
                            font-size: 35px
                        
                        .weekly 
                            margin-left: 12.5%
                        .totally
                            margin-left: 12.5%

                    .time-selection
                        display: flex
                        position: absolute
                        right: 340px
                        top: 440px
                        color: #F2F0C9
                        font-size: 16px

                        .fa-caret-left
                            margin-right: 10px
                            cursor: pointer

                        .fa-caret-right
                            margin-left: 10px
                            cursor: pointer

                    .statistics__chart
                        margin-top: 120px

                .content__setting
                    width: 460px
                    height: 380px
                    z-index: 5
                    margin-top: 280px
                    margin-left: 200px

                    .setting-space
                        width: 100%
                        height: 40%
                        margin-bottom: 76px
                        color: #F2F0C9

                        .label
                            font-size: 20px
                            margin-left: 10px
                            margin-top: 5px
                            margin-bottom: 20px

                        .setting-box
                            font-size: 16px
                            margin-left: 40px
                            margin-bottom: 10px
                            border-bottom: 1px dashed #F2F0C9
                            width: 90%
                            height: 30px
                            line-height: 30px
                            display: flex

                            .time-setting
                                margin-left: 55%

                            .fixed-setting
                                margin-left: 59%

                            .toggle
                                margin-left: 78%

                            .toggle-fixed
                                margin-left: 71%

                            .fa-angle-down
                                margin-left: 15px
            
            .mid
                left: 50%
                transform: translate(-50%, 0)
                height: 400px
                width: 400px
                background-color: transparent
                color: #F2F0C9
                text-align: center

                .wrapper
                    width: 200px
                    height: 400px
                    position: absolute
                    top: 0
                    overflow: hidden

                    .circle_animation
                        width: 384px
                        height: 384px
                        border: 8px solid rgba(162, 5, 5, 0.39)
                        border-radius: 50%
                        position: absolute
                        top: 0
                        transform: rotate(-135deg)

                    .circle-right
                        border-top: 8px solid #F2F0C9
                        border-right: 8px solid #F2F0C9
                        right: 0

                    .animate-right
                        animation: rightTurn 1500s linear infinite

                    .circle-left
                        border-bottom: 8px solid #F2F0C9
                        border-left: 8px solid #F2F0C9
                        left: 0

                    .animate-left
                        animation: leftTurn 1500s linear infinite

                @keyframes rightTurn
                    0%
                        transform: rotate(-135deg)

                    50%
                        transform: rotate(45deg)
                    
                    100%
                        transform: rotate(45deg)

                @keyframes leftTurn
                    0%
                        transform: rotate(-135deg)
                    
                    50%

                        transform: rotate(-135deg)
                        
                    100%
                        transform: rotate(45deg)
                .right
                    right: 0
                
                .left
                    left: 0


                .content
                    font-size: 25px
                    opacity: 1 !important
                    margin-top: 85px

                .time
                    font-size: 72px
                    margin-top: 30px
                    letter-spacing: 15px
            
            .extend
                left: 15%
                transform: translate(-15%, 0)

            .action
                margin-top: 55px
                margin-left: 95px
                width: 300px
                height: 70px
                display: flex
                opacity: 0.7

                .circle__small
                    height: 50px
                    width: 50px
                    border-radius: 50px
                    border: 1px solid #F2F0C9
                    margin-right: 25px
                    cursor: pointer
                    line-height: 50px

                    .fa-pause
                        display: none

                .working
                    .fa-play
                        display: none

                    .fa-pause
                        display: initial

            .mission
                width: 230px
                height: 40px
                position: absolute
                z-index: 2
                top: 330px
                left: 50px
                border-bottom: 1px solid #F2F0C9
                color: #F2F0C9
                font-size: 18px
                line-height: 40px
                opacity: 0.4
                display: flex

                .svg-inline--fa
                    line-height: 40px
                    padding-top: 10px
                    margin-left: 40px
                    cursor: pointer

            .none
                display: none


        &__footer
            height: 28px
            font-size: 24px
            color: #F2F0C9
            opacity: 0.5
            text-align: center
            margin-top: 65px

    .extend
        transform: translate(-25%, 0)
</style>

