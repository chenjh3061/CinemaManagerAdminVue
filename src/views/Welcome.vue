<template>
    <div id="welcome">
        <div id="overlay">
            <div id="bk">欢迎访问影院管理后台</div>
        </div>
    </div>
    
</template>

<script>
export default {
    name: "Welcome",
};
</script>

<style scoped>
#welcome {
    background: url("../assets/welcome.jpg");
    background-size: cover;
    height: 100%;
    width: 100%;

}
#overlay {
    width: 100%;
    height: 100%;
    position: auto;
    top: 0;
    left: 0;
    background: linear-gradient(rgba(255, 255, 255, 0.5), rgba(255, 255, 255, 0.5));
  }
#bk {
    color: black;
    margin: 0px;
    text-align: center;
    font-size: 50px;
    padding-top: 120px;
}
</style>