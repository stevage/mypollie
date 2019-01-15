<template lang="pug">
    #boundary-info
        br
        | Selected boundary:
        h3.boundary-name {{ boundaryName }}
        #rep-info(v-if="representative")
            | Representative
            br
            h3 {{ representative.first_name }} {{ representative.last_name }}
            img(:src="'https://www.openaustralia.org.au/' + representative.image")
            br
            #rep-history(v-if="rephistory")
                ul
                    li(v-for="office in rephistory.offices")
                        | {{ office.position }}

                | Attended {{ rephistory.votes_attended }} / {{ rephistory.votes_possible }} votes.
                br
                | Crossed the floor: {{ rephistory.rebellions }} times.

</template>

<script>
    let lastboundary;
    const fedreps = require('@/fed_reps.json');
    import axios from 'axios';
    export default {
        data: () => ({
            boundary: undefined,
            rephistory: undefined
        }), computed: {
            boundaryName() {
                return this.boundary && this.boundary.name.toLowerCase();
            },
            representative() {
                if (window.BoundaryType.current === 'federal' && this.boundary) {
                    const rep = fedreps.find(rep => rep.constituency.match(new RegExp(this.boundary.name, 'i')));
                    return rep;
                }
            }
        }, watch: {
            boundary(newBoundary) {
                if (!newBoundary) {
                    return;
                }
                if (!lastboundary || newBoundary.name !== lastboundary.name) {
                    lastboundary = newBoundary;
                    const cors = 'https://stevecors.glitch.me/'
                    axios.get(cors + `https://theyvoteforyou.org.au/api/v1/people/${this.representative.person_id}.json`, {
                        params: { key: 'IlYhN+j1i7rGaTu/Slht' }
                    }).then(({data}) => {
                        console.log(data);
                        this.rephistory = data;
                    });
                }
            }
        }, created() {
            window.BoundaryInfo = this;
        }
        
    }
</script>

<style scoped>
#boundary-info {
    padding:1em;
}
.boundary-name {
    text-transform:capitalize;
}
</style>