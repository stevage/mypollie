<template lang="pug">
    #boundary-type
        | Boundary type:
        br
        input#type-federal(type="radio" v-model="current" value="federal")
        label(for="type-federal") Federal
        br
        input#type-state(type="radio" v-model="current" value="state")
        label(for="type-state") State
</template>

<script>
    export default {
        created() {
            window.BoundaryType = this;
        },data: () => ({
            current: 'federal'
        }), watch: {
            current(type) {
                window.Map.setBoundaryType(type);
            }
        }
    }
</script>

<style scoped>

</style>