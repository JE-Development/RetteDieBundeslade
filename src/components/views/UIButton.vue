<template>

  <button class="ui-button center-horizontal" @click="click">
    <p style="margin-top: 2px">{{title}}</p>
  </button>
  <button class="ui-button center-horizontal shadow absolute on-bottom" @click="click">

  </button>

</template>


<script>

export default {
    name: "UIButton",
    data() {
        return {
        };
    },

    props:{
        title: String,
    },

    created() {
    },
    mounted() {
    },



    methods: {

        click(){
            this.$emit("clicked")
        },


        getCookies(key){
            return this.$cookies.get(key);
        },
        setCookies(key, value){
            return this.$cookies.set(key, value, 2147483647);
        },
    }
}
</script>