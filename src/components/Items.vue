<template>
	<div id="item-main" class="">
	
		<b-card class="controls">
			<div class="row">
				<div class="col-sm-8 col-xs-12"> 
					<div>
						<label class="d-inline-block">Number of Items</label>
						<input type="number" class="form-control d-inline-block" v-model="num_to_generate">
					</div>
					<div>
						<label class="d-inline-block">Wealth</label>
						<select class="form-control d-inline-block" v-model="selected_wealth">
							<option v-for="x in wealth_levels" v-bind:value="x">{{x}}</option> 
						</select>
					</div>
				</div> 
				<div class="col-sm-4 col-xs-12">
					<button class="btn btn-outline-dark" v-on:click="generate_items()">Generate Items</button>
				</div>
			</div>
			
		</b-card>
		
		<div class="mx-auto" style="width:80%; margin-top:3rem;">
            <div class="card " >
				<div class="card-body">
                    <div class="row " style="" v-for="x in generated_items">
                        <div class="col-xs-12">
                            {{x}}
                        </div>
                    </div>
                </div>
            </div>
		</div>
		
		<div class="d-none">
			<Books ref="books"></Books>
		</div>
		
	</div> 
	
</template>

<script>

import json_item_data from "../data/items_data.json";
import Books from "./Books.vue";

export default {
	name: 'app-main',
	components: {
		Books
	},
	data () {
		return {
			item_data: null,
			selected_wealth: 'Average',
			wealth_levels: ["Poor", "Average", "Rich"],
			num_to_generate: 10,
			generated_items: []
		} 
	},
	created: function () {
		var self = this;
		self.item_data = json_item_data;
		self.generate_items();
	},
	methods: {
		generate_items: function () {
			var self = this;
			self.generated_items = [];
			for(var i = 0; i < self.num_to_generate; i++) {
				var foo = self.generate_item(self.selected_wealth);
				self.generated_items.push(foo);
			}
		},
		generate_item: function (pwealth = null, include = [], avoid = ["Food"]) {
			var self = this;
			var item = "";
			var wealth = (pwealth) ? (pwealth) : (self.selected_wealth);
			
			var filtered_items = self.item_data["Items"].filter(function (item) {
				var allowed = item["Tags"].includes(wealth);
				var i, l = 0;
				while ((i <= include.length) && (allowed == true)) {
					allowed = allowed && item["Tags"].includes(include[i])
					i++;
				}
				
				while ((l <= avoid.length) && (allowed == true)) {
					allowed = allowed && !item["Tags"].includes(avoid[l]);
					l++;
				}
			
				return allowed;
			});
			
			item = filtered_items[Math.floor(Math.random() * filtered_items.length)]['Description'];
			
			var regex = /\{(\w*)\}/i;
			var mod = item.match(regex);
			
			if (mod) {
				while (mod) {
					item = item.replace(mod[0], self.random_modifier(mod[1]))
					mod = item.match(regex);
				}
			}
			
			var re2 = /\%(\w*)\%/i;
			var mod2 = item.match(re2);
			
			if (mod2) {
				switch (mod2[1]) {
					case "BookName":
						var name = self.$refs.books.generate_book();
						item = item.replace(mod2[0], name)
						break;
					default:
						// just remove non-matching substitutions
						item = item.replace(mod2[0], '')
						break;
				}
			}
			
			item = item.trim();
			item = item.charAt(0).toUpperCase() + item.slice(1);
			
			return item;
		},
		random_modifier: function (key) {
			var self = this;
			var foo = "";
			var list = self.item_data[key].filter(function (item) {
				return item["Tags"].indexOf(self.selected_wealth) != -1;
			});
			var i = list[Math.floor(Math.random() * list.length)];
			foo = i['Description']
			
			return foo;
		}
		
	},
	computed: {
		filtered_items: function () {
			var self = this;
			return self.item_data["Items"].filter(function (item) {
				return item["Tags"].indexOf(self.selected_wealth) != -1;
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
