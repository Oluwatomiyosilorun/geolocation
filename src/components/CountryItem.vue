<template>
    <div class="country_item">
        <img :src="country.flags.svg"  class="flag"/>
        <p class="country_name"> {{country.name.common}} </p>
    </div>
</template>

<script>
export default {
    props:{
        country:{
            type: Object
        }
    }
}
</script>

<style scoped>
    .country_item{
        display: flex;
        align-items: center;
        margin-bottom: 10px;
    }

    .flag{
        width: 20px;
        height: 20px;
    }

    .country_name{
        font-size: 14px;
    }
</style>