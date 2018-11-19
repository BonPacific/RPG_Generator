<template>
	<div id="list-main" class="">
		
		<div class="mx-auto" style="width:80%; margin-top:3rem;">
			{{list}}
		</div>

	</div> 
	
</template>

<script>

import json_name_data from "../name_data.json";

export default {
	name: 'app-main', 
	data () {
		return {
			list: {
				'Locations': [],
				'Names': [],
				'Books': [],
				'Items': [],
				'NPCs': []
			}
		} 
	},
	created: function () {
		var self = this;
		window.eventBus.$on('add_to_list', function(type, data) { 
			self.list[type].push(data);
		});
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
