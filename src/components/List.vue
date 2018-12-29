<template>
	<div id="list-main" class="">
		<b-card>
            <div class="mx-auto" style="width:90%; margin-top:3rem;">
                <div class="row">
                    <div class="col-xs-6 col-sm-6 col-md-6 col-lg-4" style="padding-bottom:1rem;" v-for="x in list_data['person']" :key="x.name">
                        <div class="card " >
                            <div class="card-body " >
                                <PersonTemplate :person="x"></PersonTemplate>
                            </div>
                        </div>
                    </div>
				</div>
            </div>
        </b-card>
	</div> 
	
</template>

<script>
import PersonTemplate from "./templates/Person.vue";

export default {
    name: 'app-main',
    components: {
        PersonTemplate
    },
    props: [
		'list_data'
	],
	created: function () {
		var self = this;
	},
	methods: {
		
	},
	computed: {
		
	}
  
}
</script>

<style scoped> 
  label {
	min-width:12rem;
 }
 
  select, input {
	min-width:15rem;
 }
</style>
