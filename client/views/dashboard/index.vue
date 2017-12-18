<template>
  <div class="content">
    <div>
      <table>
        <tr style="background-color: #00D1B2">
          <td width="30%" height="100%"><font color="#FFFFFF" size="4"><center>To Do</center></font></td>
          <td width="30%" height="100%"><font color="#FFFFFF" size="4"><center>In progress</center></font></td>
          <td width="30%" height="100%"><font color="#FFFFFF" size="4"><center>Done</center></font></td>
        </tr>
        <tr style="background-color: #FFFFFF">
          <td>
            <div v-for = "message in todo">
              <div v-if = "message.status == 'ยังไม่ได้ทำ'" class="notification is-danger">
                &nbsp;&nbsp;&nbsp;{{ message.msg }}
              </div>
            </div>
          </td>
          <td>
            <div v-for = "progress in progress">
              <div v-if = "progress.status == 'กำลังทำ'" class="notification is-warning">
                &nbsp;&nbsp;&nbsp;{{ progress.msg }}
              </div>
            </div>
          </td>
          <td>
            <div v-for = "dodone in done">
              <div v-if = "dodone.status == 'ทำเสร็จแล้ว'" class="notification is-success">
                &nbsp;&nbsp;&nbsp;{{ dodone.msg }}
              </div>
            </div>
          </td>
        </tr>
      </table>
    </div>

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
    axios.get('https://graph.facebook.com/138501810233037?fields=feed&access_token=EAACEdEose0cBAHCzbTgeq2mZBuuOmH28lizd8drOZBscqxQd6pMT2iODdSSZAdAySbFTcZACiXhkXQt9s71diPqpkYwYlokCdzP7VNF3jJZBgnuV5jeEQJ9HU5xICAG0zzkL06xi6oj5Dy4y1eK7AMDT8FfigbzfrEwJZCv4AZC2JQmGz9pWSavzv0vQZBWEFzMZD')
    .then(response => {
      this.posts = response.data
      // console.log(this.posts.feed.data)
      // let cut = posts => this.posts.filter(this.posts.feed.data.message.substr(0, 5) === '#ToDo')

      this.posts.feed.data.forEach(function (message) {
        if (message.message.substr(0, 5) === '#ToDo') {
          let newmessage = {
            id: message.id,
            msg: message.message.substr(6),
            status: 'ยังไม่ได้ทำ'
          }
          // console.log(comment.id)
          that.todo.push(newmessage)
          that.progress.push(newmessage)
          that.done.push(newmessage)
          // console.log(that.test.id)
          // that.todo.push(message.message.substr(6))
          // that.todo.push(message.id)
          // that.feedid = message.id
          // console.log(that.feedid)
        }
      })
    })
    axios.get('https://graph.facebook.com/138501810233037?fields=feed{comments}&access_token=EAACEdEose0cBAHCzbTgeq2mZBuuOmH28lizd8drOZBscqxQd6pMT2iODdSSZAdAySbFTcZACiXhkXQt9s71diPqpkYwYlokCdzP7VNF3jJZBgnuV5jeEQJ9HU5xICAG0zzkL06xi6oj5Dy4y1eK7AMDT8FfigbzfrEwJZCv4AZC2JQmGz9pWSavzv0vQZBWEFzMZD')
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
          if (progress.message === '#start') {
            let result = that.todo.find(item => item.id === comment.id)
            result.status = 'กำลังทำ'
            that.todo.push(result)
            // console.log(that.test.id)
          }
          if (progress.message === '#end') {
            let result = that.todo.find(item => item.id === comment.id)
            result.status = 'ทำเสร็จแล้ว'
            that.todo.push(result)
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

</style>
