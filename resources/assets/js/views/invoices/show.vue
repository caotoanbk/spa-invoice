<template>
	<div class="panel" v-if="show">
		<div class="panel-heading">
			<span class="panel-title">{{ model.number }}</span>
			<div>
				<router-link to="/invoices" class="btn">Back</router-link>
				<router-link to="`/invoices/${model.id}/edit`" class="btn">Edit</router-link>
				<button class="btn btn-error">Delete</button>
			</div>
		</div>
		<div class="panel-body">
			<div class="document">
				<div class="row">
					<div class="col-6">
						<strong>To:</strong>
						<div>
							<span>{{ model.customer.text}}</span>
							<pre>{{model.customer.address}}</pre>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>
<script type="text/javascript">
	import Vue from 'vue'
	import {get, byMethod} from '../../lib/api'

	export default {
		data () {
        return {
            show: false,
            model: {
                items: [],
                customer: {}
            }
        }
    },
    beforeRouteEnter(to, from, next) {
        get(`/api/invoices/${to.params.id}`)
            .then((res) => {
            	console.log(res)
                next(vm => vm.setData(res))
            })
    },
    beforeRouteUpdate(to, from, next) {
        this.show = false
        get(`/api/invoices/${to.params.id}`)
            .then((res) => {
                this.setData(res)
                next()
            })
    },
		methods: {
			setData(res) {
				this.show = true
				Vue.setData(this.$data, 'model', res.data.model)
			}
		}
	}
</script>