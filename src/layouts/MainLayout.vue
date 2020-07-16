<template>
	<q-layout view="lHh Lpr lFf">
		<q-header elevated>
			<q-toolbar>
				<q-btn
					flat
					dense
					round
					@click="leftDrawerOpen = !leftDrawerOpen"
					icon="menu"
					aria-label="Menu"
				/>
				<div class="row justify-center">
					<img class="logo" alt="Logo The New York Times" src="~assets/The_New_York_Times_Logo.svg">
				</div>

			</q-toolbar>
		</q-header>

		<q-drawer
			v-model="leftDrawerOpen"
			show-if-above
			bordered
			content-class="bg-grey-1"
		>
		 <q-list>
				<q-item-label header>Buscador de noticias NYTimes</q-item-label>

				<q-item to="/buscador" exact>
					<q-item-section avatar>
						<q-icon name="search" />
					</q-item-section>

					<q-item-section>
						Buscador
					</q-item-section>
				</q-item>

				<q-item to="/acercade" exact>
					<q-item-section avatar>
						<q-icon name="person" />
					</q-item-section>

					<q-item-section>
						Acerca de
					</q-item-section>
				</q-item>
			</q-list>
		</q-drawer>

		<q-page-container>
			<router-view />
		</q-page-container>
	</q-layout>
</template>

<style scoped>
	.row{
		width: 100%;
	}
	.logo{
		height: 5vh;
	}
</style>

<script>

export default {
	name: 'MainLayout',

	data () {
		return {
			leftDrawerOpen: false
		}
	}
}
</script>
