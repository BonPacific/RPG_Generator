<template>
	<div id="npc-main" class="">
        <b-card class="controls">
            <div>
                <button class="btn btn-outline-dark float-right" v-on:click="generate_npcs()">Generate</button>
                <button class="btn btn-outline-dark" v-on:click="controls_collapse = !controls_collapse">Settings&nbsp;&nbsp;<span class="fa" v-bind:class="{'fa-angle-up': controls_collapse == true, 'fa-angle-down': controls_collapse == false}"></span></button>
            </div>
            <div id="controls-collapse" v-if="controls_collapse">
                <hr/>
                <div class="row">
                    <div class="col-md-12">
                        <label class="">Number of NPC's</label>
                        <div class="" style="">
                            <input type="number" class="form-control" v-model="num_to_generate">
                        </div>
                    </div>
                </div>
                <div class="row">
                    
                    <div class="col-md-6"> 
                        <h5><span>Age</span> <span class="float-right">Probability</span></h5>
                        <ul class="">
                            <li class="d-block" v-for="x in life_stages" :key="x.Description">
                                <span class="text-muted">{{x.Description}}</span>
                                <input class="d-inline float-right" value="true" type="checkbox" v-model="x.Selected"/>
                            </li>
                            <button class="btn btn-sm btn-light" v-on:click="set_all(life_stages, false)">Unselect All</button>
                            <button class="btn btn-sm btn-light" v-on:click="set_all(life_stages, true)">Select All</button>
                        </ul>
                    </div> 
                    <div class="col-md-6"> 
                        <h5><span>Wealth</span> <span class="float-right">Probability</span></h5>
                        <ul class="">
                            <li class="d-block" v-for="x in wealth_level" :key="x.Description">
                                <span class="text-muted">{{x.Description}}</span>
                                <input class="d-inline float-right" value="true" type="checkbox" v-model="x.Selected"/>
                            </li>
                            <button class="btn btn-sm btn-light" v-on:click="set_all(wealth_level, false)">Unselect All</button>
                            <button class="btn btn-sm btn-light" v-on:click="set_all(wealth_level, true)">Select All</button>
                        </ul>
                    </div> 
                    <div class="col-md-6"> 
                        <h5><span>Gender</span> <span class="float-right">Probability</span></h5>
                        <ul class="">
                            <li class="d-block" v-for="x in available_gender" :key="x.Description">
                                <span v-bind:for="x.Description" class="text-muted">{{x.Description}}</span>
                                <input v-bind:id="x.Description" class="d-inline float-right" value="true" type="checkbox" v-model="x.Selected"/>
                                
                            </li>
                            <button class="btn btn-sm btn-light" v-on:click="set_all(available_gender, false)">Unselect All</button>
                            <button class="btn btn-sm btn-light" v-on:click="set_all(available_gender, true)">Select All</button>
                        </ul>
                    </div> 
                    <div class="col-md-6"> 
                    <h5><span>Species</span> <span class="float-right">Probability</span></h5> 
                        <ul class="">
                            <li class="d-block" v-for="x in available_species" :key="x.Description">
                                <!--  v-bind:class="{'d-none': advanced == false, 'd-inline': advanced == true}"  -->
                                <input v-bind:id="x.Description" class="float-right" style="margin:0 0 0 1rem;" type="number" step="any" v-model="x.Probability"/>
                                <input v-bind:id="x.Description+'check'" class="d-inline float-right" value="true" type="checkbox" v-model="x.Selected"/>
                                <label v-bind:for="x.Description+'check'" class="text-muted">{{x.Description}}</label>
                                
                            </li>
                            <button class="btn btn-sm btn-light" v-on:click="set_all(available_species, false)">Unselect All</button>
                            <button class="btn btn-sm btn-light" v-on:click="set_all(available_species, true)">Select All</button>
                        </ul>
                    </div> 
                </div>
                <!-- div class="row">
                    <div class="float-right">
                        
                    </div>
                </div>< -->
                
            
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
			available_gender: [],
			available_species: [],
			loading: true,
			controls_collapse: false,
			advanced: false
		} 
	},
	created: function () {
		var self = this;
		self.npc_data = json_npc_data;
		self.npc_data['LifeStages'].forEach(function (stage) {
			self.$set(stage, 'Selected', true);
			self.life_stages.push(stage);
        });
        
		self.npc_data['Gender'].forEach(function (n) {
			self.$set(n, 'Selected', true);
			self.available_gender.push(n);
		});
        
		self.npc_data['Species'].forEach(function (n) {
			self.$set(n, 'Selected', true);
			self.available_species.push(n);
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
			// self.generate_npcs();
		});
	},
	methods: {
		generate_npcs: function () {
            var self = this;
            this.$nextTick(function () {
                self.generated_npcs = [];
                for(var i = 0; i < self.num_to_generate; i++) {
                    var foo = self.random_npc();
                    self.generated_npcs.push(foo);
                }
                
                self.loading = false;
            });
		},
		random_npc: function (params = {wealth: null, age: null, gender: null}) {
			var self = this;
            console.log('begin npc');
            var npc = {};
            npc.appearance = [];
			
            npc.meta = [];
            
            //Gender
            if (params.hasOwnProperty('gender') && params.gender != null) {
                npc.gender = params.gender;
            } else {
                npc.gender = self.random_modifier_p({"key": 'Gender'})['Description'];
            }
			
			//Age
			if (params.hasOwnProperty('age') && params.age != null) {
				npc.age = self.random_modifier_p({"key": "LifeStages", "include": age});
			} else {
				npc.age = self.selected_ages[Math.floor(Math.random() * self.selected_ages.length)]
			}
			
			//Wealth
			if (params.hasOwnProperty('wealth') && params.wealth != null) {
				npc.wealth = self.random_modifier_p({"key": "Wealth", "include":wealth});
			} else {
				npc.wealth = self.npc_data["Wealth"][Math.floor(Math.random() * self.npc_data["Wealth"].length)]
			}

			
			//Occupation
			var filtered_professions = self.npc_data["Professions"].filter(function (x1) {
				return (x1["Tags"].includes(npc.wealth.Description) && x1["Tags"].includes(npc.age['Description']));
			});
			npc.profession = filtered_professions[Math.floor(Math.random() * filtered_professions.length)]['Description']
			
			//Species
			var species_raw = self.random_modifier_p({"local": self.available_species});
			npc.species = species_raw['Description'];
            
            //Name
            var name_tags = [npc.gender];
            if (Math.random() <= self.npc_data.Settings.SpeciesNameProbability) {
                if (species_raw.hasOwnProperty("Names")) {
                    var sel = species_raw["Names"][Math.floor(Math.random() * species_raw["Names"].length)];
                    name_tags.push(sel);
                } else {
                    name_tags.push(npc.species);
                }
                
            } 
            npc.name = self.$refs.child_name.generate(name_tags, [], 'GivenWhole', 'SurnameWhole');
			
			//Relatives
			var num_relations = Math.floor(Math.random() * 5)
			
			for(var l = 0; l < num_relations ; l++) {
				
			}
			
			//Life Events
			npc.events = [];
			var num_events = Math.floor(Math.random() * npc.age.MaxLifeEvents)
			var filtered_events = self.npc_data["LifeEvents"].filter(function (x) {
				return (x["Tags"].includes(npc.age['Description']));
			});
			for(var l = 0; l < num_events ; l++) {
                var l_event = self.random_modifier_p(
                    {
                        "key":'LifeEvents', 
                        "include": [npc.age['Description']]
                    });
				// var l_event = filtered_events[Math.floor(Math.random() * filtered_events.length)];
				
				var event_desc = l_event.Description;
				
				event_desc = event_desc.replace('%States%', (l_event['States'][Math.floor(Math.random() * l_event['States'].length)]));
				
				var regex = /\{(\w*)\}/i;
				var regex2 = /\#(\w*)\#/i;
				var mod = event_desc.match(regex);
				var mod2 = event_desc.match(regex2);
				
				if (mod) {
					while (mod) {
                        var k = self.random_modifier_p({
                            'key': mod[1]
                        });
                        // var k = self.random_modifier_p({
                        //     'key': mod[1], 
                        //     'include' : npc.age.Description
                        // });
                        if (k.hasOwnProperty('Addendum')) {
                            npc[k["Addendum"]["Field"]].push(k["Addendum"]["Value"]);
                        }
						event_desc = event_desc.replace(mod[0], k['Description'])
						mod = event_desc.match(regex);
					}
				}
				if (mod2) {
					while (mod2) {
						event_desc = event_desc.replace(mod2[0], npc[mod2[1]])
						mod2 = event_desc.match(regex);
					}
				}
				npc.events.push(event_desc)
			}
			
			//Physical Description
			
			
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
                    console.log('mod1');
                    var sel = self.random_modifier_p(
                        {"key": mod1[1], "include" : [npc.gender]}
                    );
                    console.log(hair, mod1[0], sel["Description"]);
					hair = hair.replace(mod1[0], sel["Description"]);
					mod1 = hair.match(regex);
				}
			}
			
			var mod2 = beard.match(regex);
			if (mod2) {
				while (mod2) {
                    console.log('mod2');
					beard = beard.replace(mod2[0], self.random_modifier_p(
                        {"key": mod2[1], "include" : [npc.gender]}
                    )["Description"]);
					mod2 = beard.match(regex);
				}
			}
			
			npc.hair = hair;
			npc.beard = beard;
		
			
			// Appearance
			var app_list = self.npc_data['Appearance']
            var app = self.random_modifier_p({'key': 'Appearance'})['Description'];
			var mod_a = app.match(regex);
		
			if (mod_a) {
				while (mod_a) {
					
					var list = self.npc_data[mod_a[1]].filter(function (x) {
						return x["Tags"].includes(npc.gender) && x["Tags"].includes(npc.age.Description) && x["Tags"].includes(npc.wealth.Description);
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
						disfigurement = disfigurement.replace(mod_d[0], self.random_modifier_p(
                            {"key": mod_d[1], "include" : npc.gender}))
						mod_d = disfigurement.match(regex);
					}
				}
				
				npc.appearance.push(disfigurement);
            }
            
            
			// Items
			var coins = '';
			var items_npc = [];
			var item_require = []
			var num_items = 1 + Math.floor(Math.random() * 2) + npc.age['ItemModifier'] + npc.wealth['ItemModifier'];
			
			for (var i = 0; i < num_items; i++) {
				items_npc.push(self.$refs.child_item.generate_item(npc.wealth.Description, item_require, npc.age['ItemAvoid']));
			}
			
			var coin_mod = npc.age['CoinModifier'] * npc.wealth['CoinModifier'];
			
			coins = coins.concat(Math.ceil(coin_mod * (-1 + Math.floor(Math.random() * 3 + Math.random() * 3)))).concat('gp, ')
			coins = coins.concat(Math.ceil(npc.age['CoinModifier'] * (3 + Math.floor(Math.random() * 8 + Math.random() * 12)))).concat('sp, ')
			coins = coins.concat(Math.ceil(npc.age['CoinModifier'] * (6 + Math.floor(Math.random() * 6)))).concat('cp')
			
			items_npc.push(coins);
			
			npc.items = items_npc;

			return npc;
		},
		// random_modifier: function (key, tag) {
		// 	var self = this;
		// 	var foo = "";
		// 	var list = null
		// 	if (tag.lenth > 0) {
		// 		list = self.npc_data[key].filter(function (item) {
		// 			return item["Tags"].includes(tag);
		// 		});
		// 	} else {
		// 		list = self.npc_data[key];
		// 	}
		// 	foo = list[Math.floor(Math.random() * list.length)]
			
		// 	return foo;
        // },
        random_modifier_p: function (param_object) {
            var key = ""
            var local = []
            var include = []
            var avoid = []
            if (param_object.hasOwnProperty('key')) {
                key = param_object["key"];
            }
            if (param_object.hasOwnProperty('local')) {
                local = param_object["local"];
            }
            if (param_object.hasOwnProperty('include')) {
                include = param_object["include"];
            }
            if (param_object.hasOwnProperty('avoid')) {
                avoid = param_object["avoid"];
            }

			var self = this;
            var foo = "";
            var list = []

            if (key != "") {
                if (!(key in self.npc_data)) {
                    alert('Attempted to access undefined key "'+ key +'" in data');
                }
                list = self.npc_data[key].filter(function (x0) {
                    var allowed = true;
                    var i = 0;
                    var l = 0;
                    while ((i < include.length) && (allowed == true)) {
                        allowed = allowed && x0["Tags"].includes(include[i]);
                        i++;
                    }
                    
                    while ((l <= avoid.length) && (allowed == true)) {
                        allowed = allowed && !x0["Tags"].includes(avoid[l]);
                        l++;
                    }
                    return allowed;
                });
            } else if (local != []) {
                list = local.filter(function (x) {
                    return x.Selected;
                });
            } else {
                alert("Must declare one of 'key' or 'local' in random_modifier_p()");
            }

            if (list.length == 0) {
                alert("No entries found for selected tags");
                self.loading = false;
                return null;
            }

            var total_prob = list.reduce(function (total, x) {
                if (x.hasOwnProperty['Probability']) {
                    var prob = x['Probability'];
                } else {
                    var prob = 1;
                }
                return total + prob;
            }, 0)+1;

            var selected = Math.floor(Math.random() * total_prob)
            var total_processed = 0;
            var sel_item = 0

            list.some(function (x, index, array) {
                if (x.hasOwnProperty['Probability']) {
                    var prob = x['Probability'];
                } else {
                    var prob = 1;
                }
                total_processed += prob;
                if (selected <= total_processed) {
                    sel_item = index;
                    return true;
                } else {
                    return false;
                }
            });

            var foo = list[sel_item];
			return foo;
		},
        set_all: function (list, value) {
			list.forEach(function (x) {
				x.Selected = value;
			});
		},
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
    input[type=number] {
        min-width:3rem;
        width:3.5rem;
        max-width:12rem;
        border:1px solid #ccc;
        padding: 0 .5rem;
    }
    input {
        height:2rem;
    }
    .controls li {
        height:2rem;
    }
</style>
