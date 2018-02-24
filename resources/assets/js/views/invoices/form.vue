<template>
	<div>
		<div class="panel" v-if="show">
			<div class="panel-heading">
				<span class="panel-title">{{title}} Invoice</span>
			</div>
		</div>
		<div class="panel-body">
			<div class="row">
				<div class="col-12">
					<div class="form-group">
						<label>Customer</label>
						<typeahead :url="customerURL" :initialize="form.customer"
							@input="onCustomer"></typeahead>
					</div>
				</div>
				<div class="col-6">
					<div class="form-group">
						<label>
							Number
							<small>Auto Generated</small>
						</label>
						<span class="form-control">{{form.number}}</span>
					</div>
				</div>
				<div class="col-6">
					<div class="form-group">
						<label for="">
							Reference
							<small>Optional</small>
						</label>
						<input type="text" class="form-control" v-model="form.reference">
						<small class="error-control" v-if="errors.reference">
							{{errors.reference[0]}}
						</small>	
					</div>
				</div>
			</div>
			<div class="row">
				<div class="col-6">
					<div class="form-group">
						<label for="">Date</label>
						<input type="date" class="form-control" v-model="form.date">
						<small class="error-control" v-if="errors.date">
							{{errors.date[0]}}
						</small>
					</div>
				</div>
				<div class="col-6">
					<div class="form-group">
						<label for="">Due Date</label>
						<input type="date" class="form-control" v-model="form.due_date">
						<small class="error-control" v-if="errors.due_date">
							{{errors.due_date[0]}}
						</small>
					</div>
				</div>
			</div>
			<hr>
			<table class="form-table">
				<thead>
					<tr>
						<th>Item Description</th>
						<th>Unit Price</th>
						<th>Qty</th>
						<th>Total</th>
					</tr>
				</thead>
				<tbody>
					<tr v-for="(item, index) in form.items">
						<td class="w-14">
							<typeahead :url="productURL" :initialize="item.product"
								@input="onProduct(index, $event)"></typeahead>
							<small class="error-control" v-if="errors[`items.${index}.product_id`]">
								{{errors[`items.${index}.product_id`][0]}}
							</small>
						</td>
						<td class="w-4">
							<input type="text" class="form-control" v-model="item.unit_price">
							<small class="error-control" v-if="errors[`items.${index}.unit_price`]">
								{{errors[`items.${index}.unit_price`][0]}}
							</small>
						</td>
						<td class="w-2">
							<input type="text" class="form-control" v-model="item.qty">
							<small class="error-control" v-if="errors[`items.${index}.qty`]">
								{{errors[`items.${index}.qty`][0]}}
							</small>
						</td>
						<td class="w-4">
							<span class="form-control">
								{{Number(item.qty) * Number(item.unit_price) | formatMoney}}
							</span>
						</td>
						<td>
							<span class="form-control" @click="removeItem(index)">&times;</span>
						</td>
					</tr>
				</tbody>
				<tfoot>
					<tr></tr>
				</tfoot>
			</table>
		</div>
	</div>
</template>
<script>
	import Vue from 'vue'
	import {get, byMethod } from '../../lib/api'
	import { Typeahead } from '../../components/typeahead'

	function initialize(to) {
		let urls = {
			'create': `/api/invoices/create`,
			'edit': `/api/invoices/${to.params.id}/edit`
		}
		return urls[to.meta.mode]
	}
	export default {
		components: { Typeahead },
		data () {
			return {
				form: {},
				errors: {},
				isProcessing: false,
				show: false,
				resource: '/invoices',
				store: '/api/invoices',
				method: 'POST',
				title: 'Create',
				productURL: '/api/products',
				customerURL: '/api/customers'
			}
		},
		beforeRouteEnter(to, from, next) {
			get(initialize(to))
				.then((res) => {
					next(vm => vm.setData(res))
				})
		},
		beforeRouteUpdate(to, from, next) {
			this.show = false
			get(initialize(to))
				.then((res) => {
					this.setData(res)
					next()
				})
		},
		methods: {
			setData(res) {
				Vue.set(this.$data, 'form', res.data.form)

				if(this.$route.meta.mode === 'edit') {
					this.store = `/api/invoices/${this.$route.params.id}`
					this.method = 'PUT'
					this.title = 'Edit'
				}

				this.show = true
			},
			onCustomer() {

			},
			onProduct() {

			},
			removeItem() {

			}
		}
	}
</script>