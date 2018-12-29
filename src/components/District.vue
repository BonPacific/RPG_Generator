<template>
  <div id="district-main">
	<!-- <button type="button" style="border-bottom:none;" class="btn btn-outline-dark"  -->
	 <!-- v-b-toggle.config_form>Config</button> -->
	<!-- <b-collapse visible id="config_form" class=""> -->
		<b-card class="controls">
			<div class="row">
				<div class="col-md-6">
					<div class="row">
						<label class="col-md-4">District Type</label>
						<div class="col-md-8">
								<select class="form-control" v-model="selected_type">
									<option v-for="x in district_types" v-bind:value="x.Description">{{x.Description}}</option> 
								</select>
						</div>
					
					</div>
					<div class="row"> 
						<label class="col-md-4">Wealth</label> 
						<div class="col-md-8">
								<select class="form-control d-inline-block" v-model="selected_wealth">
									<option v-for="x in district_data['Wealth']" v-bind:value="x.Description">{{x.Description}}</option> 
								</select>
						</div>
					</div>
				</div>
				<div class="col-md-6">
					<div class="row"> 
						<label class="col-md-4">Tags</label> 
						<!-- <ul class="col-md-8 tags">
							<li class="form-check" v-for="x in available_tags" :key="x.Description">
								<input v-bind:id="x.Description" class="form-check-input" value="true" type="checkbox" v-model="x.Selected"/>
								<label v-bind:for="x.Description" class="form-check-label text-muted">{{x.Description}}</label>
								
							</li>
							<button class="btn btn-sm btn-light" v-on:click="set_all(available_tags, false)">Unselect All</button>
							<button class="btn btn-sm btn-light" v-on:click="set_all(available_tags, true)">Select All</button>
						</ul> -->
					</div> 
				</div> 
			</div>
			<div class="">
					<button class="btn btn-outline-dark float-right" v-on:click="generate_district()">Generate</button>
			</div>
		</b-card>
	<!-- </b-collapse> -->

    <pre>
        {{generated_district}}
    </pre>
  </div>
</template>

<script>

import json_district_data from "../data/district_data.json";

export default {
    name: 'app-main',
    data () {
        return {
            generated_district: {},
            selected_type: 'Artisan',
            selected_wealth: 'Average',
            district_types: []
        } 
    },
    created: function () {
        var self = this;
        self.district_data = json_district_data;
        self.district_types = self.district_data['DistrictType']
    },
    methods: {
        generate_district: function () {
            console.log('generate_district');
            var self = this;
            var dist = {};
            dist.type = self.district_types.filter(function (foo) {
				return foo.Description == self.selected_type;
			})[0]
            dist.wealth = self.district_data["Wealth"].filter(function (foo) {
				return foo.Description == self.selected_wealth;
            })[0]
            
            dist.type.LocationTypes.forEach(function (x) {
                
            });

            self.generated_district = dist;
        },
        random_modifier: function (key, tags = []) {
			console.log(key);
			var self = this;
			var selected_random_modifier = null;
			var list = Array.from(self.district_data[key]);
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
        },
        randomInt: function (min, max) {
            return Math.floor(Math.random() * (max - min + 1)) + min;
        }
    },
    computed: {

    }
}
</script>

<style scoped>  

</style>
