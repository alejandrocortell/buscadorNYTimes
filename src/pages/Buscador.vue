<template>
	<q-page>
		<div class="row">
			<q-input outlined v-model="palabra" label="Búsqueda" class="col"/>
			<q-input outlined v-model="min" label="Fecha mínima" placeholder="YYYYMMDD" class="col"/>
			<q-input outlined v-model="max" label="Fecha máxima" placeholder="YYYYMMDD" class="col"/>
			<div class="col">
				<p class="mostrar">Recientes primero </p>
				<q-btn-toggle
					v-model="orden"
					spread
					class="toggle"
					no-caps
					rounded
					unelevated
					toggle-color="primary"
					color="white"
					text-color="primary"
					:options="[
					{label: 'Si', value: 'newest'},
					{label: 'No', value: 'oldest'}
					]"
				/>
			</div>
			<q-btn :loading="loading" color="primary" @click="comprobarFormulario" label="Buscar" class="col"/>
		</div>
		<p v-if="error" class="texto-error">{{ error }}</p>
		<q-list v-if="noticias.length > 0">
			<q-infinite-scroll @load="onLoad" :offset="250">
				<q-item v-for="noticia in noticias" :key="noticia.url" class="item">
					<q-item-section class="col-3" top>
						<q-item-label>
							<img :src="noticia.img" :alt="noticia.headline">
						</q-item-label>
					</q-item-section>
					<q-item-section class="col-9" top>
						<q-item-label><h2>{{ noticia.headline }}</h2></q-item-label>
						<q-item-label><span class="fecha">{{ noticia.date }} </span></q-item-label>
						<q-item-label><span class="autor">{{ noticia.author }}</span></q-item-label>
						<q-item-label><p>{{ noticia.snippet }}</p></q-item-label>
						<q-item-label><a :href="noticia.url" target="blank">{{ noticia.url }}</a></q-item-label>
					</q-item-section>
				</q-item>
			</q-infinite-scroll>
			<!-- Se muestra un círculo de carga si faltan noticias por mostrar
			y se ve mientras se realiza la peticion ajax -->
			<div v-if="faltanNoticias" class="row justify-center">
				<q-spinner
					color="primary"
					size="3em"
					:thickness="3"
				/>
			</div>
		</q-list>
	</q-page>
</template>

<style scoped>
	.row{
		margin: 10px 20px
	}
	.col{
		margin: 0px 5px;
	}

	.mostrar{
		font-weight: bold;
		margin-bottom: 0px;
	}

	.q-btn-group{
		margin-top: 0px;
	}

	.texto-error{
		color: rgb(126, 7, 7);
		margin: 10px;
	}

	.item{
		border-bottom: 1px solid #dddddd;
	}

	h2{
		font-size: 1.5rem;
		line-height: 1;
		font-weight: bold;
		margin-top: 0px;
		margin-bottom: 0px;
	}

	.autor{
		font-size: 0.9rem;
		color: #383838;
		font-style: italic;
		margin-bottom: 10px;
	}

	p{
		margin-bottom: 5px;
	}

	a{
		text-decoration: none;
		color: #2f388c;
	}

	a:hover{
		color: #1a1f4d;
	}

	img{
		width: 100%;
	}
</style>

<script>
import axios from "axios";
export default {
	name: 'Buscador',

	data(){
		return{
			loading: false,
			error: "",
			noticias: [],
			palabra: "",
			min: "", 
			max: "",
			orden: "newest",
			pagina: 0,
			faltanNoticias: true
		}
	},

	methods: {
		comprobarFormulario(){
			this.error = "";
			this.faltanNoticias = true;

			//Comprobación de los inputs y se muestra el mensaje de error
			if(this.palabra != ""){
				var regex = /^\d{8}$/;
				if(regex.test(this.min) && regex.test(this.max)){
					if(this.min < this.max){
						this.noticias = [];
						this.realizarBusqueda();
					}
					else{
						this.error = "La fecha máxima debe ser mayor a la mínima";
					}
				}else{
					this.error = "Introduce una fecha en formato YYYYMMDD";
				}
			}else{
				this.error = "Introduce una palabra para realizar la búsqueda";
			}
		},
		realizarBusqueda(){
			this.loading = true;
			this.error = "";

			axios
			.get("https://api.nytimes.com/svc/search/v2/articlesearch.json?" +
				"q=" + this.palabra +
				"&fq=headline:('" + this.palabra + "')" +
				"&begin_date=" + this.min +
				"&end_date=" + this.max +
				"&sort=" + this.orden +
				"&page=" + this.pagina +
				"&api-key=/*Inserta tu APIKEY aqui*/")
			.then(response => {
				for(var x in response.data.response.docs){
					var img = "";
					if(response.data.response.docs[x].multimedia[0] != undefined){
						img = "https://www.nytimes.com/" + response.data.response.docs[x].multimedia[0].url;
					}

					var noticia = {
						snippet: response.data.response.docs[x].snippet,
						url: response.data.response.docs[x].web_url,
						headline: response.data.response.docs[x].headline.main,
						author: response.data.response.docs[x].byline.original,
						date: response.data.response.docs[x].pub_date.substr(0, 10),
						img: img
					};

					this.noticias.push(noticia);
				}

				//Si no hay noticias se muestra el mensaje de error
				if(this.noticias.length == 0){
					this.error = "No se encuentran noticias";
				}

				// Se comprueba si tenemos en el array el mismo número de noticias para
				// poner en false el faltan noticias
				if(this.noticias.length == response.data.response.meta.hits){
					this.faltanNoticias = false;
				}
			});
			
			this.loading = false;
		},
		onLoad (index, done) {
			// Se comprueba si faltan noticias para evitar llamadas innecesarias al servidor
			if(this.faltanNoticias){
				setTimeout(() => {
					this.pagina++;
					this.realizarBusqueda();
					done();
				}, 3000);
			}
		}
	}
}
</script>