<template>
	<div id="find_us_container">
	    <div  v-if="pageBanner" class="page_header" v-bind:style="{ backgroundImage: 'url(' + pageBanner.image_url + ')', marginBottom:'0px'}">
			<div class="site_container">
				<div class="header_content">
					<h1>Find Us</h1>
				</div>
			</div>
		</div>  
		<!-- for some reason if you do not put an outer container div this component template will not render -->
		<div class="site_container">
			<div class="row text-left" style="height: 300px; margin:30px auto;">
			    <iframe src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d2440.613977882536!2d-113.81218554887757!3d52.28670976114537!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x537455b49f248801%3A0xe01229fcdbb6c8f4!2sParkland+Mall!5e0!3m2!1sen!2sca!4v1548868823076" width="100%" height="300" frameborder="0" style="border:0" allowfullscreen></iframe>
				<google-map :property="property" :zoom="_.toNumber('17')"></google-map>
			</div>
		</div>
	</div>
</template>

<script>
    define(["Vue", "vuex", "vue!google_map"], function(Vue, Vuex, moment, GoogleMapAPI) {
        return Vue.component("find-us-component", {
            template: template, // the variable template will be injected,
            data: function() {
                return {
                    success_subscribe: false,
                    currentPage: null,
                    pageBanner: null
                }
            },
            created(){
                this.loadData().then(response => {
                    this.currentPage = response[0].data;
                    var temp_repo = this.findRepoByName('Contact Us Banner');
                    if(temp_repo) {
                        this.pageBanner = temp_repo.images[0];
                    }
                });
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'findRepoByName'
                ]),
            },
            methods: {
                loadData: async function() {
                    try {
                        // avoid making LOAD_META_DATA call for now as it will cause the entire Promise.all to fail since no meta data is set up.
                        let results = await Promise.all([this.$store.dispatch("getData", "repos")]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
            }
        });
    });
</script>