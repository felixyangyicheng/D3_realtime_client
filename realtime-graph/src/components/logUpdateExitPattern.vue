<template>
  <div>
    <div class="update-enter">
      <h2>Update Enter</h2>
    </div>
    <div class="update-exit">
      <h2>Update Exit</h2>
    </div>
  </div>
</template>
<script>
import * as d3 from "d3";
import * as signalR from "@microsoft/signalr";
export default {
  data() {
    return {};
  },
  mounted() {
    let log = [];
    let connection = new signalR.HubConnectionBuilder()
      .withUrl("http://localhost:5153/loghub/")
      .configureLogging(signalR.LogLevel.Information)
      .build();
    connection.on("refreshlog", (data) => {
      console.log(data);
      log.push(data);
    });
    connection.start();

    let p = d3.select(".update-enter").selectAll("p");
    let update = p.data(log);
    let enter = update.enter();
    update.text(function (d, i) {
      return "update: " + d.value + " ,index: " + i;
    });
    let pEnter = enter.append("p");
    pEnter.text(function (d, i) {
      return "enter: " + d.value + " ,index: " + i;
    });

    // update exit
    let dataset2 = [];
    let p2 = d3.select(".update-exit").selectAll("p");
    let update2 = p2.data(dataset2);
    let exit = update2.exit();
    update2.text(function (d, i) {
      return "update: " + d.value + " ,index: " + i;
    });
    exit.text(function () {
      return "exit";
    });

    // add data
  },
};
</script>
<style></style>
