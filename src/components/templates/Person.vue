<template>
	<div class="">
		<h5 class="card-title"><span v-if="person.title">{{person.title}}</span> {{person.name}}</h5>
		<h6 class="card-subtitle mb-2 text-muted">{{person.species}}, {{person.age.Description}}, {{person.gender}} </h6>
		<h6 class="card-subtitle mb-2 text-muted"><strong>Profession: </strong>{{person.profession}} </h6>
		<!-- <h6 class="card-subtitle mb-2 text-muted">{{person.age.Description}}, {{person.gender}}</h6> -->
		<h6 class="card-subtitle mb-2 text-muted"><strong>Wealth:</strong> {{person.wealth.Description}}</h6>
		<h6 class="card-subtitle mb-2 text-muted"><strong>Appearance</strong></h6>
		<ul class="card-text">
			<li>
				Wears their hair {{person.hair}}
			</li>
			<li v-if="person.beard.length > 0">
				Has {{person.beard}}
			</li>
			<li v-for="appearance in person.appearance" :key="appearance">
				{{appearance}}
			</li>
		</ul>
		<h6 class="card-subtitle mb-2 text-muted"><strong>Possessions</strong></h6>
		<ul class="" v-if="person.items">
			<li class="d-block" v-for="item in person.items" :key="item">
				{{item}}
			</li>
		</ul>
		<h6 class="card-subtitle mb-2 text-muted"><strong>Life Events</strong></h6>
		<ul class="">
			<li class="d-block" v-for="event in person.events"  :key="event">
				{{event}}
			</li>
		
		</ul>
	</div>
	
</template>

<script>
export default {
	name: 'person-template', 
	props: [
		'person'
	],
	data () {
		return {} 
	}
}
</script>
