<template>
	<div id="item-main" class="">
	
		<b-card class="controls">
			<div class="row">
				<div class="col-md-6"> 
					<div class="row">
						<label class="col-md-4">Number of Books</label>
						<div class="col-md-8">
							<input type="number" class="form-control" v-model="num_to_generate">
						</div>
					</div>
				</div>
				<div class="col-md-6">
					<div class="row">	
						<label class="col-md-4">Style</label>
						<ul class="col-md-8 tags">
							<li class="form-check" v-for="x in styles">
								<input v-bind:id="x.Description" class="form-check-input" value="true" type="checkbox" v-model="x.Selected"/>
								<label v-bind:for="x.Description" class="form-check-label text-muted">{{x.Description}}</label>
								
							</li>
							<!-- <button class="btn btn-sm btn-outline-dark" v-on:click="deselect_all_tags()">Unselect All</button>
							<button class="btn btn-sm btn-outline-dark" v-on:click="deselect_all_tags()">Select All</button> -->

						</ul>
						
					</div>
				</div>
				
			</div>
			<div class="">
				<button class="btn btn-outline-dark float-right" v-on:click="generate_books()">Generate</button>
			</div>
		</b-card>
		
		<div class="mx-auto results" style="">
			<div class="card " >
				<div class="card-body">
					<div class="" style="" v-for="x in generated_items" :key="x">
						<div class="col-xs-12" style="text-transform:capitalize;">
							{{x}}
						</div>
					</div>
				</div>
			</div>
		</div>
		
		<div class="d-none">
			<Name ref="child_name"></Name>
		</div>
		
	</div> 
	
</template>

<script>

import json_book_data from "../data/books_data.json";
import Name from "./Name.vue";

export default {
	name: 'app-main', 
	components: {
		Name
	},
	data () {
		return {
			book_data: null,
			styles: [],
			num_to_generate: 10,
			generated_items: [],
			loading: true
		} 
	},
	created: function () {
		var self = this;
		self.book_data = json_book_data;
		self.book_data.Tags.forEach(function (tag) {
			tag.Selected = true;
			self.styles.push(tag);
		});
		
	},
	mounted: function () {
		var self = this;
		this.$nextTick(function () {
			//using nextTick to make sure the Names component is fully mounted and ready.
			self.generate_books();
		});
	},
	methods: {
		generate_books: function () {
			var self = this;
			self.generated_items = [];
			for(var i = 0; i < self.num_to_generate; i++) {
				var foo = self.generate_book();
				self.generated_items.push(foo);
			}
			
			self.loading = false;
		},
		generate_book: function () {
			var self = this;
			var selected_styles = self.styles.filter(function (foo) {
				return foo.Selected == true
			})
			var style = selected_styles[Math.floor(Math.random() * selected_styles.length)]['Description'];
			var item = "";
			var filter_list = self.filtered_books(style);
			item = filter_list[Math.floor(Math.random() *
				filter_list.length)]['Description']
			
			var re1 = /\{(\w*)\}/i;
			var mod = item.match(re1);
			
			if (mod) {
				while (mod) {
					item = item.replace(mod[0], self.random_modifier(mod[1], style))
					
					// checkfor any more substitutions
					mod = item.match(re1);
				}
			}
			var re2 = /\%(\w*)\%/i;
			var mod2 = item.match(re2);
			
			if (mod2) {
				switch (mod2[1]) {
					case "Name":
						var name = self.$refs.child_name.generate_name();
						item = item.replace(mod2[0], name)
						break;
					case "FirstName":
						var name = self.$refs.child_name.generate_name(true, false);
						item = item.replace(mod2[0], name)
						break;
					case "LastName":
						var name = self.$refs.child_name.generate_name(false, true);
						item = item.replace(mod2[0], name)
						break;
					default:
						// just remove non-matching substitutions
						item = item.replace(mod2[0], '')
						break;
				}
			}
			
			item = item.trim();
			// item = item.charAt(0).toUpperCase() + item.slice(1); 
			return item;
		},
		random_modifier: function (key, style) {
			var self = this;
			var foo = "";
            
            try {
			var list = self.book_data[key].filter(function (item) {
				return item["Tags"].includes(style);
            });
            } catch (error) {
                console.log('error', error);
                console.log('key', key);
                console.log('style', style);
            }
			foo = list[Math.floor(Math.random() * list.length)]['Description']
			
			return foo;
		},
		filtered_books: function (style) {
			var self = this;
			return self.book_data["Books"].filter(function (item) {
				return item["Tags"].includes(style);
			});
		}

	},
	computed: {
		
		
	}
  
}
</script>

<style scoped> 

</style>
