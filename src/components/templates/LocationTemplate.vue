<template>
	<div class="">
		<h3 class="card-title">{{location.type.Name}}
            <div class="btn btn-dark-outine float-right" title="Save">
                <i class="far fa-plus-square"></i>
            </div>
            <div class="btn btn-dark-outine float-right" title="View Owner"
            v-if="location.Owner" v-on:click="location.tab_owner = !location.tab_owner">
                <i class="fa-user" v-bind:class="{fas: location.tab_owner == true, far: location.tab_owner == false}"></i>
            </div>
        </h3>

        <div v-if="location.tab_owner == false">
            <h5 class="card-title">{{location.type.Description}}</h5>
            <h6 class="card-subtitle mb-2 telocationt-muted"></h6>
            <h6 class="card-subtitle mb-2"><strong>Wealth: </strong><span class=" telocationt-muted">{{location.wealth.Description}}</span> </h6>
            
        </div>
        
        <div v-if="location.tab_owner == true">
            <PersonTemplate :person="location.Owner"></PersonTemplate>
        </div>
	</div>
	
</template>

<script>

import PersonTemplate from "./Person.vue";

export default {
    name: 'location-template', 
    components: [
        PersonTemplate
    ],
	props: [
		'location'
	],
	data () {
		return {} 
	}
}
</script>
