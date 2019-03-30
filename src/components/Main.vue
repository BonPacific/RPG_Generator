<template>
  <div id="app-main" class="app-main">
  
	<div v-if="view_screen == 'District'">
		<District>
		</District>
	</div>
	<div v-if="view_screen == 'Name'">
		<Name>
		</Name>
	</div>
	<div v-if="view_screen == 'Items'">
		<Items>
		</Items>
	</div>
	<div v-if="view_screen == 'Book'">
		<Books>
		</Books>
	</div>
	<div v-if="view_screen == 'NPC'">
		<NPC>
		</NPC>
	</div>
	<div v-if="view_screen == 'Location'">
		<Location>
		</Location>
	</div>
	<div v-if="view_screen == 'List'">
		<List :list_data="list_data">
		</List>
	</div>
	
  </div>
</template>

<script>

import District from "./District.vue"
import Name from "./Name.vue"
import Items from "./Items.vue"
import Books from "./Books.vue"
import List from "./List.vue"
import NPC from "./NPC.vue"
import Location from "./Location.vue"

export default {
  name: 'app-main',
  data () {
    return {
		district_data: null,
		name_data: null,
        district_config: {},
        list_data: {
            'person': []
        },
		view_screen: 'District'
    } 
  },
  components: {
		District,
		Books,
		Name,
		NPC,
		List,
		Items,
		Location
  },
  created: function () {
		var self = this;
        window.eventBus.$on('view_screen_change', function(data) { self.view_screen = data; });
        window.eventBus.$on('add_to_list', function(type, data) { 
            console.log('on: add_to_list', type, data);
			self.list_data[type].push(data);
		});
  },
  methods: {
		// random: function (type) {
		// 	console.log(type, typeof type);
		// 	var self = this;
		// 	var arr = Object.keys(self.district_data[type]);
		// 	var ret = arr[Math.floor(Math.random() * arr.length)]
		// 	console.log(ret);
		// 	return ret;
		// },
		// sumObjectsByKey: function(objs) {
		// 	console.log('objs', objs);
		// 	return objs.reduce((a, b) => { 
		// 	for (let k in b) { 
		// 		if (b.hasOwnProperty(k)) 
		// 		a[k] = (a[k] || 0) + b[k]; 
		// 	} 
		// 	return a; 
		// 	}, {}); 
		// }
  },
  computed: {
		totals: function () {
			var self = this;
			var values = [];
			var tags = [];
			// Iterate through selected values
			Object.keys(self.district_config).forEach(function(item) {
				console.log(item, self.district_config[item]);
				var foo = self.district_data[item][self.district_config[item]]['Values'];
				values.push(foo);
				
				if (self.district_data[item][self.district_config[item]]['Meta']['Tags']) {
					console.log('tags', self.district_data[item][self.district_config[item]]['Meta']['Tags']);
					tags = tags.concat(self.district_data[item][self.district_config[item]]['Meta']['Tags']);
					console.log(tags);
				}
			});
			
			var values = self.sumObjectsByKey(values);
			var ret = {'Values': values, "Tags": tags};
			
			return ret;
		}
  }
  
}
</script>

<style > 
 #app-main {
	padding-top:6.4rem;
 }
 
  label {
	font-weight:600;
 }
 
  select {
	min-width:15rem;
	width:100%;
	display:inline-block;
 }

.controls ul label {
	font-weight: 300;
	margin:0;
}

.controls ul li {
	line-height:24px;
}

.form-check {
	padding:0;
	padding-left:10px;
}
.results {
	width:90%; 
	margin-top:3rem;
}
</style>
