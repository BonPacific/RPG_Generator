<template>
	<div id="name-main" class="">
	
		<b-card class="controls">
			<div class="row">
				<div class="col-sm-8 col-xs-12"> 
					<div>
						<label class="d-inline-block">Number of names</label>
						<input type="number" class="form-control d-inline-block" v-model="num_to_generate">
					</div>
				</div> 
			</div>
			<div class="row">
                <div class="col-sm-12"> 
                    <label>Structure</label>
				</div>
                <div class="col-md-4 col-sm-6"> 
					<div>
						
						<select class="form-control d-inline-block" v-model="first_selected">
                            <option v-for="x in first" v-bind:key="x" v-bind:value="x">{{x}}</option>
                        </select>
					</div>
				</div> 
				<div class="col-md-4 col-sm-6"> 
					<div>
						
						<select class="form-control d-inline-block" v-model="second_selected">
                            <option v-for="x in second" v-bind:key="x" v-bind:value="x">{{x}}</option>
                        </select>
					</div>
				</div> 
			</div>
			<div class="row">
                <div class="col-md-4">
                    <label>Tags</label> 
                    <ul class="tags">
                        <li class="form-check" v-for="x in tags" :key="x.Description">
                            <input v-bind:id="x.Description" class="form-check-input" value="true" type="checkbox" v-model="x.Selected"/>
                            <label v-bind:for="x.Description" class="form-check-label text-muted">{{x.Description}}</label>
                            
                        </li>
                        <button class="btn btn-sm btn-light" v-on:click="set_all(tags, false)">Unselect All</button>
                        <button class="btn btn-sm btn-light" v-on:click="set_all(tags, true)">Select All</button>
                    </ul>
                </div> 
            </div> 
            <div class="row">
                <div class=" offset-xs-6 col-xs-6 col-md-3 offset-md-9">
					<button class="btn btn-outline-dark w100" v-bind:disabled="loading" v-on:click="function () {loading = true; generate_names();}">Generate Names</button>
                    <div v-if="loading" class="float-right"><i class="fas fa-spinner fa-pulse"></i></div>
                </div>
            </div>
		</b-card>
		
		<div class="mx-auto" style="width:80%; margin-top:3rem;">
            <div class="card " >
				<div class="card-body">
                    <div class="row " style="" v-for="x in generated_names">
                        <div class="col-xs-12">
                            {{x}}
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
	components: {
	},
	data () {
		return {
			name_data: null,
            loading: false,
            tags: [],
            first: ['GivenWhole', 'GivenConstructed', '-None-'],
            first_selected: "GivenWhole",
            second: ['SurnameWhole', 'SurnameConstructed', '-None-'],
            second_selected: "SurnameWhole",
			num_to_generate: 10,
			generated_names: []
		} 
	},
	created: function () {
		var self = this;
        self.name_data = json_name_data;
        self.tags = self.name_data["Tags"]
	},
	mounted: function () {
		var self = this;
		this.$nextTick(function () {
			//using nextTick to make sure the child components are fully mounted and ready.
			self.generate_names();
		});
	},
	methods: {
		generate_names: function () {
            var self = this;
            self.loading = true;
            self.generated_names = [];

            console.log(self.first_selected, self.second_selected);

            var f_tags = self.tags.filter(function (foo) {
				return foo.Selected == true
            })
            var sel_tags = [];
            f_tags.forEach(function (tag) {
                sel_tags.push(tag.Description);
            });
            var n = 0
			for(n; n < self.num_to_generate; n++) {
				var foo = self.generate(sel_tags);
				self.generated_names.push(foo);
            }
            self.loading = false;
		},
		generate: function (include = [], avoid = []) {
			var self = this;
            var name1;
            var name2;
            var name_full = "";
            var name1str = "";
            var name2str = "";
            
            if (self.first_selected != "-None-") {
                name1 = self.random_modifier(self.first_selected, include, avoid);
                if (name1.hasOwnProperty('Diminutive') && (Math.random() < 0.1)) {
                    name1str += name1.Diminutive;
                } else {
                    name1str += name1.Full;
                }
            }
            
            
            if (self.second_selected != "-None-") {
                name2 = self.random_modifier(self.second_selected, include, avoid);
                if (name2.hasOwnProperty('Diminutive') && (Math.random() < 0.1)) {
                    name2str += name2.Diminutive;
                } else {
                    name2str += name2.Full;
                }
            }

            console.log("name2str", name2str);

            name_full = name1str + ' ' + name2str;

            var regex = /\{(\w*)\}/i;
			var mod = name_full.match(regex);
			
			if (mod) {
				while (mod) {
                    var bar = self.random_modifier(mod[1], include, avoid).Full;
                    console.log(bar);
					name_full = name_full.replace(mod[0], bar)
					mod = name_full.match(regex);
				}
            }
			
            // name_full += name1str.charAt(0).toUpperCase() + name1str.slice(1);
            // name_full += ' ';
			// name_full += name2str.charAt(0).toUpperCase() + name2str.slice(1);
			
			return name_full;
		},
		random_modifier: function (key, include = [], avoid = []) {
            // console.log(key, include, avoid);
			var self = this;
			var foo = "";

            if (!(key in self.name_data)) {
                alert('Attempted to access undefined key "'+ key +'" in name_data');
            }
            var list = self.name_data[key].filter(function (name) {
                // console.log(name.Full, include.length, include);
				var allowed = true;
                var i = 0;
                var l = 0;
				while ((i < include.length) && (allowed == true)) {
                    // console.log('allowed: '+allowed, 'name[tags].includes('+include[i]+'): '+ name["Tags"].includes(include[i]));
                    allowed = allowed && name["Tags"].includes(include[i]);
                    
					i++;
				}
				
				while ((l <= avoid.length) && (allowed == true)) {
					allowed = allowed && !name["Tags"].includes(avoid[l]);
					l++;
				}
                // console.log(allowed);
				return allowed;
            });

            if (list.length == 0) {
                alert("No entries found for selected tags");
                self.loading = false;
                return null;
            }

            var total_prob = list.reduce(function (total, x) {return total + x['Probability']}, 0)+1;
            var selected = Math.floor(Math.random() * total_prob)
            var total_processed = 0;
            // console.log(total_prob, selected);
            var sel_name = 0
            // console.log('list length', list.length);
            list.some(function (x, index, array) {
                total_processed += x['Probability'];
                // console.log('index'+index, 'total_processed'+total_processed, 'selected'+selected);
                if (selected <= total_processed) {
                    sel_name = index;
                    return true;
                } else {
                    return false;
                }
            });
            // console.log("sel_name", sel_name);
            var foo = list[sel_name];
            // console.log(foo);
			return foo;
		},
		set_all: function (list, value) {
			list.forEach(function (x) {
				x.Selected = value;
			});
		},
	},
	computed: {
		filtered_names: function () {
			var self = this;
			return self.name_data["names"].filter(function (name) {
				return name["Tags"].indexOf(self.selected_wealth) != -1;
			});
		} 
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
