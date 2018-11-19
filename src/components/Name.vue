<template>
	<div id="name-main" class="">
	
		<b-card class="controls">
			<div class="row">
				<div class="col-sm-8 col-xs-12"> 
					<div>
						<label class="d-inline-block">Number of Names</label>
						<input type="number" class="form-control d-inline-block" v-model="num_to_generate">
					</div>
					<div>
						<label class="d-inline-block">First Name</label>
						<input type="checkbox" class="form-control d-inline-block" v-model="want_first_name">
					</div>
					<div>
						<label class="d-inline-block">Surname</label>
						<input type="checkbox" class="form-control d-inline-block" v-model="want_last_name">
					</div>
				</div> 
				<div class="col-sm-4 col-xs-12">
					<button class="btn btn-outline-dark" v-on:click="generate_names()">Generate Names</button>
				</div>
			</div>
			
		</b-card>
		
		<div class="mx-auto results" style="">
			<div class="card " >
				<div class="card-body">
					<div  style="" v-for="name in generated_names">
						<div class="col-xs-12">
					
							{{name}}
						</div>
					</div>
				</div>
			</div>
		</div>
	</div> 
	
</template>

<script>

import json_name_data from "../data/name_data.json";

export default {
	name: 'app-main', 
	data () {
		return {
			name_data: null,
			want_first_name: true,
			want_last_name: true,
			num_to_generate: 10,
			generated_names: []
		} 
	},
	created: function () {
		var self = this;
		self.name_data = json_name_data;
		self.generate_names();
		//window.eventBus.$on('request_random_name', function(id, first, last) { 
		//	var genname = self.generate_name(first, last);
		//	var response = id.concat('response_random_name');
		//	console.log("on request_random_name", response, genname);
		//	window.eventBus.$emit(response, genname);
		//});
	},
	methods: {
		generate_names: function () {
			var self = this;
			self.generated_names = [];
			for(var i = 0; i < self.num_to_generate; i++) {
				self.generated_names.push(self.generate_name(self.want_first_name, self.want_last_name));
			}
		},
		generate_name: function (bfirst = true, blast = true, bgender = null) {
			var self = this;
			var first = (bfirst) ? self.random_first() : '';
			var last = (blast) ? self.random_last() : '';
			
			return first.concat(" ").concat(last).trim();
		},
		random_first: function () {
			var self = this;
			var p = "";
			var c = "";
			var s = "";
			p = (self.name_data['Given']['Prefix'][Math.floor(Math.random() * self.name_data['Given']['Prefix'].length)]);
			if (Math.random() > .5) {
				c = (self.name_data['Given']['Constructors'][Math.floor(Math.random() * self.name_data['Given']['Constructors'].length)]);
			}
			s = (self.name_data['Given']['Suffix'][Math.floor(Math.random() * self.name_data['Given']['Suffix'].length)]);
			
			return p.concat(s);
		},
		random_last: function () {
					// console.log('random_last'); -->
			var name = '';
			var self = this;
			switch (Math.floor(Math.random() * 3)) {
				case 0:
					var name = (self.name_data['Surname']['Single'][Math.floor(Math.random() * self.name_data['Surname']['Single'].length)]);
					
					break;
				case 1:
					var foo = self.random_first();
					
					if ((Math.floor(Math.random() > .7))) {
						name = foo.concat('son');
					} else {
						name = foo.concat('dotte');
					}
					
					break;
				
				case 2:
					
					var foo = (self.name_data['Surname']['Main'][Math.floor(Math.random() * self.name_data['Surname']['Main'].length)]);
					var bar = (self.name_data['Surname']['Suffix'][Math.floor(Math.random() * self.name_data['Surname']['Suffix'].length)]);
					
					name = foo.concat(bar);
					
					break;
			}
			
			return name;
		}
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
