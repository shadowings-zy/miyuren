<script>
  import { MaterialApp, Button, AppBar } from "svelte-materialify";
  import Card from "./component/Card.svelte";
</script>

<MaterialApp>
  <AppBar class="primary-color theme--dark" style="padding: 5px 20px">
    <span slot="title">来当个谜语人吧</span>
    <div class="divider" />
    <Button outlined style="margin-bottom: 0">查看源码</Button>
  </AppBar>
  <Card />
</MaterialApp>

<style>
  .divider {
    flex-grow: 1;
  }
</style>
