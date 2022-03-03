<template>
  <div>
    <h2>General Update Pattern I</h2>
    <svg width="960" height="500"></svg>
  </div>
</template>
<script>
import * as d3 from 'd3'
import * as signalR from '@microsoft/signalr'

export default {
  data () {
    return {}
  },
  mounted () {

    let log=[];
let connection = new signalR.HubConnectionBuilder()
        .withUrl(
          "http://localhost:5153/loghub/"
        )
        .configureLogging(signalR.LogLevel.Information)
        .build()

      // Forward hub events through the event, so we can listen for them in the Vue components
      connection.on('refreshlog', (data) => {
       console.log(data)
       log.push(data)
      })


      // You need to call connection.start() to establish the connection but the client wont handle reconnecting for you!
      // Docs recommend listening onclose and handling it there.
      // This is the simplest of the strategies
    connection.start()


    let svg = d3.select('svg')
    // let width = +svg.attr('width')
    // let height = +svg.attr('height')
    // let g = svg.append('g').attr('transform', 'translate(32, ' + (height / 2) + ')')
    let g = svg.append('g').attr('transform', 'translate(20, 30)')

    function update (data) {
      // DATA JOIN
      // Join new data with old elements, if any.
      let text = g.selectAll('text')
        .data(data)

      // UPDATE
      // Join new data with old elements, if any.
      text.attr('class', 'update')

      // ENTTER
      // Create new elements as needed
      //
      // ENTER + UPDATE
      // After merging the entered elements with the update selection,
      // apply operations to both.
      text.enter().append('text')
        .attr('class', 'enter')
        .attr('x', function (d, i) { return i * 32 })
        .attr('dy', '.35em')
        .merge(text)
        .text(function (d) { return d.value })

      // EXIT
      // Remove old elements as needed.
      text.exit().remove()
    }

update(log)
    // Grad a random sample of letters from the alphabet, in alphabetical order.
    d3.interval(function () {
      update(d3.shuffle(log)
        .slice(0, Math.floor(Math.random() * 26))
        .sort())
    }, 1500)
  },

  beforeUnmount () {
    // Make sure to cleanup SignalR event handlers when removing the component
    this.$logHub.$off('refreshlog')
  }
}
</script>
<style>
text {
  /* font: bold 48px monospace; */
}
.enter {
  fill: green;
}
.update {
  fill: #333;
}
</style>
