<template>
	<div class="promo_dets_container" v-if="currentJob">
		<div class="page_header" v-if="jobBanner" v-bind:style="{ backgroundImage: 'url(' + jobBanner.image_url + ')' }">
			<div class="site_container">
				<div class="header_content caps">
					<h1>{{$t("jobs_page.jobs")}}</h1>
				</div>
			</div>
		</div>
		<div class="site_container">
			<div class="row">
				<div class="col-sm-4 promo_logo_container hidden_phone">
					<div class="image_container">
						<img v-lazy="currentJob.store.store_front_url_abs" class="image"/>
					</div>
					<div class="text-center" v-if="currentJob.store.name">
					    <div v-if="currentJob.jobable_type == 'Store'">
						    <h4 class="event_store_name caps" v-if="locale=='en-ca'">{{currentJob.store.name}}</h4>
						    <h4 class="event_store_name caps" v-else>{{currentJob.store.name_2}}</h4>
						</div>
						
						<h4 v-if="currentJob.store.phone" class="store_dets_title"> <a :href="'tel:'+currentJob.store.phone">{{currentJob.store.phone}}</a></h4>
						<h4 v-if="currentJob.store.website" class="store_dets_title"> <a :href="'//'+currentJob.store.website" target="_blank">{{$t("stores_page.store_website")}}</a></h4>
						<h4 v-if="storeHours.length >0 " class="store_dets_title">{{$t("stores_page.store_hours")}}</h4>
						<ul class="store_hours_list">
							<li v-if="storeHours" v-for="hour in storeHours">
								{{hour.day_of_week | moment("dddd", timezone)}} - {{hour.open_time | moment("h A", timezone)}} - {{hour.close_time | moment("h A", timezone)}}
							</li>
						</ul>
						<div class="store_dets_btn caps" v-if="currentJob.jobable_type == 'Store'">
							<router-link :to="'/stores/'+currentJob.store.slug"> {{$t("stores_page.store_dets_loc")}}</router-link>
						</div>
					</div>
					<div class="text-center" v-if="currentJob.jobable_type == 'Property'">
					    <h4 class="event_store_name caps" v-if="property">{{property.name}}</h4>
					    <h4 v-if="property.contact_phone" class="store_dets_title"> <a :href="'tel:'+property.contact_phone">{{property.contact_phone}}</a></h4>
					</div>
				</div>
				<div class="col-sm-8 promo_image_container text-left">
					<router-link to="/jobs"><i class="fa fa-angle-left"></i> &nbsp; {{$t("jobs_page.back_to_jobs")}}</router-link>
					<h3 class="promo_name" style="margin: 20px auto 0px;" v-if="locale=='en-ca'">{{currentJob.name}}</h3>
					<h3 class="promo_name" style="margin: 20px auto 0px;" v-else>{{currentJob.name_2}}</h3>
					<div class="row">
						<p class="promo_div_date pull-left">{{currentJob.start_date | moment("MMM D", timezone)}} - {{currentJob.end_date | moment("MMM D", timezone)}}</p>
						<social-sharing :url="shareURL(currentJob.slug)" :title="currentJob.title" :description="currentJob.description" :quote="currentJob.description_short" twitter-user="BonnieDoonSC" :media="currentJob.image_url" inline-template >
							<div class="blog-social-share pull-right" style="margin: 15px auto;">
								<div class="social_share">
									<network network="facebook">
										<i class="fa fa-facebook social_icons" aria-hidden="true"></i>
									</network>
									<network network="twitter">
										<i class="fa fa-twitter social_icons" aria-hidden="true"></i>
									</network>
								</div>
							</div>
						</social-sharing>
					</div>
					<div class="col-sm-12 no_padding">
						<div class="text-left promo_description">
							<p v-if="locale=='en-ca'" v-html="currentJob.rich_description"></p>
							<p v-else v-html="currentJob.rich_description_2"></p>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
var _extends=Object.assign||function(t){for(var r=1;r<arguments.length;r++){var e=arguments[r];for(var o in e)Object.prototype.hasOwnProperty.call(e,o)&&(t[o]=e[o])}return t};define(["Vue","vuex","moment","vue-lazy-load"],function(t,r,e,o){return t.use(o),t.component("job-details-component",{template:template,data:function(){return{currentJob:null,storeJobs:null,storeHours:null,jobBanner:null}},props:["id","locale"],beforeRouteUpdate:function(t,r,e){this.currentJob=this.findJobBySlug(t.params.id),(null===this.currentJob||void 0===this.currentJob)&&this.$router.replace("/"),e()},created:function(){var t=this;this.loadData().then(function(){t.updateCurrentJob(t.id);var r=t.findRepoByName("Jobs Banner");r&&(t.jobBanner=r.images[0]),t.jobs=t.job})},watch:{currentJob:function(){if(null!=this.currentJob){null!=this.currentJob.store&&void 0!=this.currentJob.store&&_.includes(this.currentJob.store.image_url,"missing")?this.currentJob.store.store_front_url_abs=this.property.default_logo_url:(null==this.currentJob.store||void 0==this.currentJob.store)&&(this.currentJob.store={},this.currentJob.store.store_front_url_abs=this.property.default_logo_url);var t=this,r=[],e=_.toNumber(this.currentJob.id);_.forEach(this.currentJob.store.jobs,function(o){if(_.toNumber(o)!=e){var n=t.findJobById(o);n.description_short=_.truncate(n.description,{length:70}),r.push(n)}}),this.storeJobs=r}if(this.currentJob.store){var o=[],t=this;_.forEach(this.currentJob.store.store_hours,function(r){var e=t.findHourById(r);e.order=0===e.day_of_week?7:e.day_of_week,o.push()}),this.storeHours=_.sortBy(o,[function(t){return t.order}])}}},computed:_extends({},r.mapGetters(["property","processedJobs","findJobBySlug","findJobById","timezone","findRepoByName","findHourById"]),{allJobs:function(){return this.processedJobs}}),methods:{updateCurrentJob:function(t){this.currentJob=this.findJobBySlug(t),(null===this.currentJob||void 0===this.currentJob)&&this.$router.replace("/")},loadData:function(){var t;return regeneratorRuntime.async(function(r){for(;;)switch(r.prev=r.next){case 0:return r.prev=0,r.next=3,regeneratorRuntime.awrap(Promise.all([this.$store.dispatch("getData","jobs"),this.$store.dispatch("getData","repos")]));case 3:t=r.sent,r.next=9;break;case 6:r.prev=6,r.t0=r["catch"](0),console.log("Error loading data: "+r.t0.message);case 9:case"end":return r.stop()}},null,this,[[0,6]])},shareURL:function(t){var r="http://bonniedoonshoppingcentre.com/jobs/"+t;return r}}})});
</script>