<template>
	<div id="loc-main" class="">
	
		<b-card class="controls">
			<div class="row">
				<div class="col-md-6">
					<div class="row">
						<label class="col-md-4">Number of Locations</label>
						<div class="col-md-8">
							<input type="number" class="form-control" v-model="num_to_generate">
						</div>
					</div>
					<div class="row"> 
						<label class="col-md-4">Wealth</label> 
						<ul class="col-md-8 tags">
							<li class="form-check" v-for="x in available_wealth" :key="x.Description">
								<input v-bind:id="x.Description" class="form-check-input" value="true" type="checkbox" v-model="x.Selected"/>
								<label v-bind:for="x.Description" class="form-check-label text-muted">{{x.Description}}</label>
								
							</li>
							<button class="btn btn-sm btn-light" v-on:click="set_all(available_wealth, false)">Unselect All</button>
							<button class="btn btn-sm btn-light" v-on:click="set_all(available_wealth, true)">Select All</button>
						</ul>
					</div>
				</div>
				<div class="col-md-6">
					<div class="row"> 
						<label class="col-md-4">Tags</label> 
						<ul class="col-md-8 tags">
							<li class="form-check" v-for="x in available_tags" :key="x.Description">
								<input v-bind:id="x.Description" class="form-check-input" value="true" type="checkbox" v-model="x.Selected"/>
								<label v-bind:for="x.Description" class="form-check-label text-muted">{{x.Description}}</label>
								
							</li>
							<button class="btn btn-sm btn-light" v-on:click="set_all(available_tags, false)">Unselect All</button>
							<button class="btn btn-sm btn-light" v-on:click="set_all(available_tags, true)">Select All</button>
						</ul>
					</div> 
				</div> 
			</div>
			<div class="">
                <button class="btn btn-outline-dark float-right" v-on:click="generate_locations()">Generate</button>
			</div>
		</b-card>
		
		<div class="mx-auto" style="width:90%; margin-top:3rem;">
			<div class="row" style="" >
				
				<div class="col-xs-6 col-sm-6 col-md-6 col-lg-4" style="padding-bottom:1rem;" v-for="x in generated_locations">
					<div class="card " >
						<div class="card-body">
							<LocationTemplate :location="x"></LocationTemplate>
						</div>
					</div>
				</div>
			</div>
		</div>

		<div class="d-none">
			<Name ref="child_name"></Name>
		</div>

		<div class="d-none">
			<NPC ref="child_npc"></NPC>
		</div>

		<!-- <pre>
			{{loc_data}}
		</pre> -->
		
		<!-- Nothing Past This Point! -->
	</div> 
</template>

<script>

import json_location_data from "../data/location_data.json";
import LocationTemplate from "./templates/LocationTemplate.vue";
import Name from "./Name.vue";
import NPC from "./NPC.vue";

export default {
	name: 'app-main', 
	components: {
		LocationTemplate,
		Name,
		NPC
	},
	data () {
		return {
			loc_data: null,
			available_tags: [],
			available_wealth: [],
			num_to_generate: 2,
			generated_locations: [],
			loading: true
		} 
	},
	created: function () {
		var self = this;
		self.loc_data = json_location_data;
		self.available_tags = self.loc_data["LocationTags"]
		self.available_wealth = self.loc_data["WealthLevels"]
		
	},
	mounted: function () {
		var self = this;
		this.$nextTick(function () {
			//using nextTick to make sure the Child components are fully mounted and ready.
			self.generate_locations();
		});
	},
	methods: {
		generate_locations: function () {
			var self = this;
			self.generated_locations = [];
			for(var i = 0; i < self.num_to_generate; i++) {
				var foo = self.random_location();
				foo.tab_owner = false;
				self.generated_locations.push(foo);
			}
			
			self.loading = false;
		},
		random_location: function (wealth = "", type="") {
			var self = this;
			var loc = {};

			loc.meta = [];
			loc.tab = 'location';

			var select_wealth = self.available_wealth.filter(function (foo) {
				return foo.Selected == true
			})
			
			var f_tags = self.available_tags.filter(function (foo) {
				return foo.Selected == true
			})
			
			//Wealth
			if (wealth != "") {
				loc.wealth = wealth;
			} else {
				loc.wealth = select_wealth[Math.floor(Math.random() * select_wealth.length)];
			}

			var selected_tags = [loc.wealth.Description, f_tags[Math.floor(Math.random() * f_tags.length)]['Description']];
			
			//Type
			loc.type = {};
			if (type != "") {
				loc.type = Object.assign({}, self.random_modifier("LocationType", type));
			} else {
				loc.type = Object.assign({}, self.random_modifier("LocationType", selected_tags));
			}

			var regex = /\{(\w*)\}/i;
			var mod_tags = [loc.wealth.Description];
			var name = loc.type.Name
			var mod = name.match(regex);

			if (mod) {
				while (mod) {
					var random = self.random_modifier(mod[1], mod_tags).Description;
					name = name.replace(mod[0], random);
					mod = name.match(regex);
				}
			}

			loc.type.Name = name;

			//Owner
			// if (loc.type.Owner) {
			// 	loc.Owner = self.$refs.child_npc.random_npc();
			// } else {
			// 	loc.Owner = null;
			// }
			
			
			return loc;
		},
		random_modifier: function (key, tags = []) {
			console.log(key);
			var self = this;
			var selected_random_modifier = null;
			var list = Array.from(self.loc_data[key]);
			var f_list = [];
			
			list.forEach(function (thing) {
				var test = true;
				tags.forEach(function (tag) {
					test = test && (thing["Tags"].includes(tag));
				})
				if (test) {
					f_list.push(thing);
				}
			})
			if (f_list.length < 1) {
				console.log('filtered list empty tags:', tags);
			}
				
			selected_random_modifier = Object.assign({}, f_list[Math.floor(Math.random() * f_list.length)]);
			if (key != 'LocationType') {
				console.log('==== selected_random_modifier', JSON.stringify(selected_random_modifier));
			} else {
				console.log('==== location', JSON.stringify(selected_random_modifier));
			}
			
			var regex = /\{(\w*)\}/i;
			var mod = selected_random_modifier.Description.match(regex);
			if (mod) {
				while (mod) {
					var bar = self.random_modifier(mod[1], tags);
					selected_random_modifier.Description = selected_random_modifier.Description.replace(mod[0], bar.Description);
					mod = selected_random_modifier.Description.match(regex);
				}
			}
			
			return selected_random_modifier;
		},
		set_all: function (list, value) {
			list.forEach(function (x) {
				x.Selected = value;
			});
		},
		add_to_list: function (type, value) {
			window.eventBus.$emit("add_to_list", type, value);
		}
	},
	computed: {
		selected_ages: function () {
			return this.life_stages.filter(function (foo) {return foo.Selected});
		}
	},
	watch: {
		// loc_data: function(newVal, oldVal) {
		// 	alert('loc_data changed');
		// },
	}
  
}
</script>

<style scoped> 
</style>
