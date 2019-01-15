<template lang="pug">
    #boundary-info
        br
        | Selected boundary:
        h3.boundary-name {{ boundaryName }}
</template>

<script>
    export default {
        data: () => ({
            boundary: undefined
        }), computed: {
            boundaryName() {
                return this.boundary && this.boundary.name.toLowerCase();
            }
        }, created() {
            window.BoundaryInfo = this;
        }
        
    }
</script>

<style scoped>
.boundary-name {
    text-transform:capitalize;
}
</style>