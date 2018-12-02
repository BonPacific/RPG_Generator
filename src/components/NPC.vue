<template>
	<div id="npc-main" class="">
	
		<b-card class="controls">
			<div class="row">
				<div class="col-sm-8 col-xs-12"> 
					<div class="row">
						<label class="col-md-4">Number of NPC's</label>
						<input type="number" class="form-control" v-model="num_to_generate">
					</div>
					<div class="row"> 
						<label class="col-md-4">Age</label> 
						<ul class="col-md-4">
							<li class="d-block" v-for="x in life_stages" :key="x.Description">
								<span class="text-muted">{{x.Description}}</span>
								<input class="d-inline float-right" value="true" type="checkbox" v-model="x.Selected"/>
							</li>
						</ul>
					</div> 
					<div class="row"> 
						<label class="col-md-4">Wealth</label> 
						<ul class="col-md-4">
							<li class="d-block" v-for="x in wealth_level" :key="x.Description">
								<span class="text-muted">{{x.Description}}</span>
								<input class="d-inline float-right" value="true" type="checkbox" v-model="x.Selected"/>
							</li>
						</ul>
					</div> 
				</div> 
				<div class="col-sm-4 col-xs-12">
					<button class="btn btn-outline-dark" v-on:click="generate_npcs()">Generate</button>
				</div>
			</div>
			
		</b-card>
		
		<div class="mx-auto" style="width:90%; margin-top:3rem;">
			<div class="row" style="" >
				<div class="col-xs-6 col-sm-6 col-md-6 col-lg-4" style="padding-bottom:1rem;" v-for="x in generated_npcs" :key="x.name">
					<div class="card " >
						<div class="card-body " >
							<PersonTemplate :person="x"></PersonTemplate>
						</div>
					</div>
				</div>
			</div>
		</div>
		
		<div class="d-none">
			<Name ref="child_name"></Name>
		</div>
		
		<div class="d-none">
			<Item ref="child_item"></Item>
		</div>
		
	</div> 
	
</template>

<script>

import json_npc_data from "../data/npc_data.json";
import PersonTemplate from "./templates/Person.vue";
import Name from "./Name.vue";
import Item from "./Items.vue";

export default {
	name: 'app-main', 
	components: {
		PersonTemplate,
		Name,
		Item
	},
	data () {
		return {
			npc_data: null,
			life_stages: [],
			wealth_level: [],
			num_to_generate: 10,
			generated_npcs: [],
			loading: true
		} 
	},
	created: function () {
		var self = this;
		self.npc_data = json_npc_data;
		self.npc_data['LifeStages'].forEach(function (stage) {
			self.$set(stage, 'Selected', true);
			self.life_stages.push(stage);
		});
		
		self.npc_data['Wealth'].forEach(function (stage) {
			self.$set(stage, 'Selected', true);
			self.wealth_level.push(stage);
		});
		
	},
	mounted: function () {
		var self = this;
		this.$nextTick(function () {
			//using nextTick to make sure the Names component is fully mounted and ready.
			self.generate_npcs();
		});
	},
	methods: {
		generate_npcs: function () {
			var self = this;
			self.generated_npcs = [];
			for(var i = 0; i < self.num_to_generate; i++) {
				var foo = self.random_npc();
				self.generated_npcs.push(foo);
			}
			
			self.loading = false;
		},
		random_npc: function (wealth = null, age = null, gender = null) {
			var self = this;
            console.log('begin npc');
			var npc = {};
			
			npc.meta = [];
			
			//Name
			npc.name = self.$refs.child_name.generate();
			// npc.last_name = self.$refs.child_name.generate();
			
			//Age
			if (age != null) {
				npc.age = self.random_modifier("LifeStages", age);
			} else {
				npc.age = self.selected_ages[Math.floor(Math.random() * self.selected_ages.length)]
			}
			
			//Wealth
			if (wealth != null) {
				npc.wealth = self.random_modifier("Wealth", wealth);
			} else {
				npc.wealth = self.npc_data["Wealth"][Math.floor(Math.random() * self.npc_data["Wealth"].length)]
			}

			// Items
// console.log('nps items');
// 			var coins = '';
// 			var items_npc = [];
// 			var item_require = []
// 			var num_items = 1 + Math.floor(Math.random() * 4) + npc.age['ItemModifier'] + npc.wealth['ItemModifier'];
			
// 			for (var i = 0; i < num_items; i++) {
// 				items_npc.push(self.$refs.child_item.generate_item(npc.wealth.Description, item_require, npc.age['ItemAvoid']));
// 			}
			
// 			var coin_mod = npc.age['CoinModifier'] * npc.wealth['CoinModifier'];
			
// 			coins = coins.concat(Math.ceil(coin_mod * (-1 + Math.floor(Math.random() * 3 + Math.random() * 3)))).concat('gp, ')
// 			coins = coins.concat(Math.ceil(npc.age['CoinModifier'] * (3 + Math.floor(Math.random() * 8 + Math.random() * 12)))).concat('sp, ')
// 			coins = coins.concat(Math.ceil(npc.age['CoinModifier'] * (6 + Math.floor(Math.random() * 6)))).concat('cp')
			
// 			items_npc.push(coins);
			
// 			npc.items = items_npc;
			
// 			console.log('end npc items')
			//Occupation
			var filtered_professions = self.npc_data["Professions"].filter(function (item) {
				return (item["Tags"].includes(npc.wealth.Description) && item["Tags"].includes(npc.age['Description']));
			});
			npc.profession = filtered_professions[Math.floor(Math.random() * filtered_professions.length)]['Description']
			
			//Species
			npc.species = self.npc_data['Species'][Math.floor(Math.random() * self.npc_data['Species'].length)]['Description']
			
			//Relatives
			var num_relations = Math.floor(Math.random() * 5)
			
			for(var l = 0; l < num_relations ; l++) {
				
			}
			
			//Life Events
			npc.events = [];
			var num_events = Math.floor(Math.random() * npc.age.MaxLifeEvents)
			var filtered_events = self.npc_data["LifeEvents"].filter(function (item) {
				return (item["Tags"].includes(npc.age['Description']));
			});
			for(var l = 0; l < num_events ; l++) {
				var l_event = filtered_events[Math.floor(Math.random() * filtered_events.length)];
				
				var event_desc = l_event.Description;
				
				
				event_desc = event_desc.replace('%States%', (l_event['States'][Math.floor(Math.random() * l_event['States'].length)]));
				
				var regex = /\{(\w*)\}/i;
				var regex2 = /\#(\w*)\#/i;
				var mod = event_desc.match(regex);
				var mod2 = event_desc.match(regex2);
				
				if (mod) {
					while (mod) {
						event_desc = event_desc.replace(mod[0], self.random_modifier(mod[1], npc.age.Description)['Description'])
						mod = event_desc.match(regex);
					}
				}
				if (mod2) {
					while (mod2) {
						event_desc = event_desc.replace(mod2[0], npc[mod2[1]])
						mod2 = event_desc.match(regex);
					}
				}
				
				
				if (event_desc.includes('disfigured')) {
					npc.meta.push('disfigured');
				}
				
				npc.events.push(event_desc)
			}
			
			//Physical Description
			//Gender
			npc.gender = self.npc_data['Gender'][Math.floor(Math.random() * self.npc_data['Gender'].length)]
			
			//Hair
			var f_hair = self.npc_data["Hair"].filter(function (item) {
				return item["Tags"].includes(npc.gender);
			});
			
			var f_beard = self.npc_data["FacialHair"].filter(function (item) {
				return item["Tags"].includes(npc.gender) && item["Tags"].includes(npc.age.Description);
			});
			
			var hair = f_hair[Math.floor(Math.random() * f_hair.length)]['Description'];
			
			var beard =  (f_beard.length > 0) ? f_beard[Math.floor(Math.random() * f_beard.length)]['Description'] : '';
			
			var regex = /\{(\w*)\}/i;
			var mod1 = hair.match(regex);
			
			if (mod1) {
				while (mod1) {
					hair = hair.replace(mod1[0], self.random_modifier(mod1[1], npc.gender))
					mod1 = hair.match(regex);
				}
			}
			
			var mod2 = beard.match(regex);
			if (mod2) {
				while (mod2) {
					beard = beard.replace(mod2[0], self.random_modifier(mod2[1], npc.gender))
					mod2 = beard.match(regex);
				}
			}
			
			npc.hair = hair;
			npc.beard = beard;
			npc.appearance = [];
			
			// Appearance
			var app_list = self.npc_data['Appearance']
			var app = app_list[Math.floor(Math.random() * app_list.length)];
			var mod_a = app.match(regex);
		
			if (mod_a) {
				while (mod_a) {
					
					var list = self.npc_data[mod_a[1]].filter(function (item) {
						return item["Tags"].includes(npc.gender) && item["Tags"].includes(npc.age.Description) && item["Tags"].includes(npc.wealth.Description);
					});
					var app_sel = list[Math.floor(Math.random() * list.length)];
					app = app.replace(mod_a[0], app_sel['Description']);
					mod_a = app.match(regex);
				}
			}
			
			npc.appearance.push(app);
			
			// Disfigurement
			if (npc.meta.includes('disfigured')) {
				var dis_list = self.npc_data['Disfigurement']
				var disfigurement = dis_list[Math.floor(Math.random() * dis_list.length)]['Description'];
				
				var mod_d = disfigurement.match(regex);
			
				if (mod_d) {
					while (mod_d) {
						disfigurement = disfigurement.replace(mod_d[0], self.random_modifier(mod_d[1], npc.gender))
						mod_d = disfigurement.match(regex);
					}
				}
				
				npc.appearance.push(disfigurement);
			}
			console.log('end npc');
			return npc;
		},
		random_modifier: function (key, tag) {
			var self = this;
			var foo = "";
			var list = null
			if (tag.lenth > 0) {
				list = self.npc_data[key].filter(function (item) {
					return item["Tags"].includes(tag);
				});
			} else {
				list = self.npc_data[key];
			}
			foo = list[Math.floor(Math.random() * list.length)]
			// console.log(key, foo, list);
			
			return foo;
		}
		
	},
	computed: {
		selected_ages: function () {
			return this.life_stages.filter(function (foo) {return foo.Selected});
		},
		selected_wealth: function () {
			return this.wealth_level.filter(function (foo) {return foo.Selected});
		}
	}
  
}
</script>

<style scoped> 
	.row {
		margin-bottom:1rem;
	}
</style>
