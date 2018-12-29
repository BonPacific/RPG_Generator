<template>
	<div class="">
		<h5 class="card-title">
            <span v-if="person.title">{{person.title}}</span> 
            {{person.name}}
            <span class="float-right">
                <button class="btn btn-white" title="Save to list" v-on:click="add_to_list(person)" v-if="person.saved_to_list == false">
                    <span class="fa fa-clipboard"></span>
                </button>
                <!-- <span class="fa fa-clipboard-check" v-if="person.saved_to_list == true"></span> -->
            </span>
        </h5>
		<h6 class="card-subtitle mb-2 text-muted">{{person.species}}, {{person.age.Description}}, {{person.gender}} </h6>
		<h6 class="card-subtitle mb-2 text-muted"><strong>Profession: </strong>{{person.profession}} </h6>
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
			<li class="d-block" v-for="(item, index) in person.items" :key="index">
				{{item}}
			</li>
		</ul>
		<h6 class="card-subtitle mb-2 text-muted"><strong>Life Events</strong></h6>
		<ul class="">
			<li class="d-block" v-for="(event, index) in person.events"  :key="index">
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
    },
    created () {
        this.person.saved_to_list = false;
    },
    methods: {
        add_to_list: function () {
            console.log('add_to_list');
            this.person.saved_to_list == true;
            window.eventBus.$emit("add_to_list", 'person', this.person);
        }
    }
}
</script>
