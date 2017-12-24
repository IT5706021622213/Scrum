<template>
  <div class="content">
    <div>
      <!-- To Do -->
      <div class="col-xs-4 col-md-4 col-lg-4">
        <table>
          <tr style="background-color: #00D1B2">
            <td width="30%" height="100%"><font color="#FFFFFF" size="4"><center>To Do</center></font></td>
          </tr>
        </table>
        <div class="tbl-content">
          <table>
            <tr style="background-color: #FFFFFF">
              <div v-for = "message in todo">
                <div v-if = "message.status == 'ยังไม่ได้ทำ'" class="notification is-danger">
                  &nbsp;&nbsp;&nbsp;{{ message.msg }}
                </div>
              </div>
            </tr>
          </table>
        </div>
      </div>
      <!-- End To Do -->

      <!-- In Progress -->
      <div class="col-xs-4 col-md-4 col-lg-4">
        <table>
          <tr style="background-color: #00D1B2">
            <td width="30%" height="100%"><font color="#FFFFFF" size="4"><center>In progress</center></font></td>
          </tr>
        </table>
        <div class="tbl-content">
          <table>
            <tr style="background-color: #FFFFFF">
              <div v-for = "progress in progress">
                <div v-if = "progress.status == 'กำลังทำ'" class="notification is-warning">
                  <div v-show = "progress.date == datetime">
                    Status : <font color="green"><b>On time</b></font>
                    Date : {{ progress.dmy }}
                  </div>
                  <div v-show = "progress.date != datetime">
                    Status : <font color="red"><b>Delay</b></font>
                    Date : {{ progress.dmy }}
                  </div>
                  &nbsp;&nbsp;&nbsp;{{ progress.msg }}
                  <div>
                    Name : {{ progress.name }}
                  </div>
                </div>
              </div>
            </tr>
          </table>
        </div>
      </div>
      <!-- End In Progress -->

      <!-- Done -->
      <div class="col-xs-4 col-md-4 col-lg-4">
        <table>
          <tr style="background-color: #00D1B2">
            <td width="30%" height="100%"><font color="#FFFFFF" size="4"><center>Done</center></font></td>
          </tr>
        </table>
        <div class="tbl-content">
          <table>
            <tr style="background-color: #FFFFFF">
              <div v-for = "dodone in done">
                <!-- <div v-if = "dodone.status == 'ทำเสร็จแล้ว'" class="notification is-success">
                  <div>
                    Status : <font color="green"><b>{{ dodone.dash }}</b></font>
                  </div> -->
                  <!-- <div v-show = "progress.date == progress.ondelay">
                    Status : <font color="green"><b>On time</b></font>
                  </div>
                  <div v-show = "progress.date != progress.ondelay">
                    Status : <font color="green"><b>Delay</b></font>
                  </div> -->
                  <!-- &nbsp;&nbsp;&nbsp;{{ dodone.msg }}
                  <div>
                    Name : {{ dodone.name }}
                  </div>
                </div> -->
                <article v-if = "dodone.status == 'ทำเสร็จแล้ว'" class="message is-success">
                  <div class="message-header">
                    <p>Status : <font color="green"><b>{{ dodone.dash }}</b></font></p>
                    <button class="delete" aria-label="delete"></button>
                  </div>
                  <div class="message-body">
                    &nbsp;&nbsp;&nbsp;{{ dodone.msg }}
                    <div>
                      Name : {{ dodone.name }}
                    </div>
                  </div>
                </article>
              </div>
            </tr>
          </table>
        </div>
      </div>
      <!-- End Done -->
    </div>
    <h1>Fixed Table header</h1>
    งานทั้งหมด : {{ count }} <br>
    To Do : {{ sumtodo }}<br>
    In Progress : {{ sumprogress }} <br>
    Done : {{ donecount }}
  </div>
</template>

<script>
import Chart from 'vue-bulma-chartjs'
import axios from 'axios'

export default {
  components: {
    Chart
  },

  data () {
    return {
      data: [300, 50, 100],
      posts: [],
      comments: [],
      errors: [],
      todo: [],
      todoo: [],
      progress: [],
      done: [],
      datetime: '',
      testy: 'hello',
      count: 0,
      todocount: 0,
      sumtodo: 0,
      progresscount: 0,
      sumprogress: 0,
      donecount: 0,
      feedid: 0
    }
  },
  computed: {
    chartData () {
      return {
        labels: [
          'Red',
          'Blue',
          'Yellow'
        ],
        datasets: [{
          data: this.data,
          backgroundColor: [
            '#FF6384',
            '#36A2EB',
            '#FFCE56'
          ]
        }]
      }
    }
  },
  mounted () {
    let that = this
    that.datetime = Date().substr(8, 2)
    setInterval(() => {
      // https://github.com/vuejs/vue/issues/2873
      // Array.prototype.$set/$remove deprecated (use Vue.set or Array.prototype.splice instead)
      this.data.forEach((item, i) => {
        this.data.splice(i, 1, Math.ceil(Math.random() * 1000))
      })
    }, 1024)
  },
  created () {
    let that = this
    axios.get('https://graph.facebook.com/138501810233037?fields=feed&access_token=EAACEdEose0cBAK0POtZAQElkfbAyAiC0BfyP9ZBn2jyG4vcGMS5VBE80zHent0Yey7QiyhqI4yaAgVvEUzGFAs11vZBR9TM8Nr3fk7dpIa2xljMqm47xBzL9iJKkLqeoUdE5n9NTCu4Lf4ZAjurD0v8ynKOAAWY0LQ5Q6UGj8Mohs0NHZAbfY9wHU6mHFtHUZD')
    .then(response => {
      this.posts = response.data
      // console.log(this.posts.feed.data)
      // let cut = posts => this.posts.filter(this.posts.feed.data.message.substr(0, 5) === '#ToDo')

      this.posts.feed.data.forEach(function (message) {
        if (message.message.substr(0, 5) === '#ToDo') {
          let newmessage = {
            id: message.id,
            name: '',
            msg: message.message.substr(6),
            status: 'ยังไม่ได้ทำ',
            date: '',
            dmy: '',
            ondelay: '',
            dash: ''
          }
          // console.log(comment.id)
          that.todo.push(newmessage)
          that.progress.push(newmessage)
          that.done.push(newmessage)
          that.count++
          // console.log(that.test.id)
          // that.todo.push(message.message.substr(6))
          // that.todo.push(message.id)
          // that.feedid = message.id
          // console.log(that.feedid)
        }
      })
    })
    axios.get('https://graph.facebook.com/138501810233037?fields=feed{comments}&access_token=EAACEdEose0cBAK0POtZAQElkfbAyAiC0BfyP9ZBn2jyG4vcGMS5VBE80zHent0Yey7QiyhqI4yaAgVvEUzGFAs11vZBR9TM8Nr3fk7dpIa2xljMqm47xBzL9iJKkLqeoUdE5n9NTCu4Lf4ZAjurD0v8ynKOAAWY0LQ5Q6UGj8Mohs0NHZAbfY9wHU6mHFtHUZD')
    .then(response => {
      this.comments = response.data
      // console.log(this.comments.feed.data[0].comments.data[0].from.name)
      // console.log(this.comments.feed.data[0].comments.data[0].message)
      this.comments.feed.data.forEach(function (comment) {
        // let result = that.todo.find(item => item === comment.id)
        // console.log(result);
        // that.progress.push(result)
        // console.log(comment.id)
        comment.comments.data.forEach(function (progress) {
          // console.log(progress.message);
          // console.log(progress.from.name);

          // comment start
          if (progress.message === '#start') {
            let result = that.todo.find(item => item.id === comment.id)
            result.status = 'กำลังทำ'
            result.date = progress.created_time.substr(8, 2)
            result.dmy = progress.created_time.substr(0, 10)
            result.name = progress.from.name
            that.progresscount++
            that.sumprogress = that.progresscount-that.donecount
            that.todo.push(result)
            // console.log(that.todo)
            // console.log(that.datetime)
            // console.log(that.test.id)
          }
          // End comment start
          if (progress.message === '#end') {
            let result = that.todo.find(item => item.id === comment.id)
            result.status = 'ทำเสร็จแล้ว'
            result.ondelay = progress.created_time.substr(8, 2)
            if (result.date === result.ondelay) {
              result.dash = 'On time'
            }
            else {
              result.dash = 'Delay'
            }
            that.donecount++
            that.todo.push(result)
            console.log(that.todo)
            // console.log(that.test.id)
          }
        })
        // console.log(that.progress)
      })
    })
    .catch(e => {
      this.errors.push(e)
    })
  }
}
</script>

<style lang="scss" scoped>
  h1{
    font-size: 30px;
    color: #000;
    text-transform: uppercase;
    font-weight: 300;
    text-align: center;
    margin-bottom: 15px;
  }

  .tbl-content{
    height:300px;
    overflow-x:auto;
    margin-top: 0px;
    border: 1px solid rgba(255,255,255,0.3);
  }

  /* for custom scrollbar for webkit browser*/
  ::-webkit-scrollbar {
    width: 6px;
  }
  ::-webkit-scrollbar-track {
    -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,0.3);
  }
  ::-webkit-scrollbar-thumb {
    -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,0.3);
  }
</style>
