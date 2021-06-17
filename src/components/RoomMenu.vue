<template>
	<div class="menu">
		<ul>
			<li class="room">
				<div>
					<input
						type="text"
						v-model="search"
						v-on:keyup="room_filter"
						placeholder="Search title.."
						size="48"
					/>
				</div>
			</li>
			<li
				v-for="item in filtered_rooms"
				v-bind:key="item.id"
				v-bind:name="item.name"
				class="room"
				onclick="closeMenu()"
			>
				<div
					v-if="item.name === item.name.toUpperCase()"
					class="program"
				>
					{{ item.name }}
				</div>
				<div v-else class="roomLink">
					<router-link
						:to="{
							name: item.route,
							params: item.classroom
						}"
						>{{ item.name }}</router-link
					>
				</div>
			</li>
		</ul>
	</div>
</template>

<script>
export default {
	name: "MenuItems",
	props: {
		rooms: {
			type: Array,
			default: () => []
		},
		filtered_rooms: {
			type: Array,
			default: () => []
		}
	},
	// data: () => ({
	//   filtered_rooms: this.rooms
	// }),
	methods: {
		room_filter() {
			if (this.search != "") {
				// console.log(this.rooms)
				this.filtered_rooms = this.rooms.filter(
					i =>
						i.name
							.toLowerCase()
							.includes(this.search.toLowerCase()) &&
						i.name.toUpperCase() != i.name
				);
			} else {
				this.filtered_rooms = this.rooms;
			}
		}
	}
};
</script>

<style>
.menu,
.menu * {
	box-sizing: border-box;
}
.menu {
	position: fixed;
	width: 50vw;
	height: 100vh;

	top: 0;

	overflow-y: scroll;
	overflow-x: hidden;
	background-color: rgba(255, 255, 255, 0.8);
	text-align: left;
	z-index: 4;
	transform-origin: 0% 0%;
	/* transform: translate(-100%, 0);
	transition: transform 0.5s cubic-bezier(0.77, 0.2, 0.05, 1); */
}

.menu li {
	/* padding: 1rem; */
	font-size: 1.1em;
}
.menu li div {
	padding: 0.5rem;
}
.menu li .program {
	font-size: 1.3em;
	background-color: white;
	text-align: center;
	width: 100%;
	/* padding-left: 0; */
	padding: 1.2em;
}
</style>
