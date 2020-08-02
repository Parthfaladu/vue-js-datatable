<template>
	<table align="center" width="100%" class="table table-hover" :class="className">
		<thead>
			<tr>
				<slot></slot>
			</tr>
		</thead>
		<tbody></tbody>
	</table>
</template>
<script>
import $ from "jquery";
import 'datatables.net/js/jquery.dataTables.js';

export default {    
	name: 'VueJsDatatable',
	props: {
		columns: {
			type: Array|null,
			default: () => []
		},
		url: {
			type: String,
			default: null	
		},
		type: {
			type: String,
			default: 'GET'
		},
		searching: {
			type: Boolean,
			default: true
		},
		lengthChange: {
			type: Boolean,
			default: true
		},
		order: {
			type: Array,
			default: () => []
		},
		className: {
			type: String,
			default: ''
		},
        paging: {
            type: Boolean,
            default: true
        },
        ordering: {
            type: Boolean,
            default: true
        },
        serverSide: {
            type: Boolean,
            default: true
		},
		authorization: {
			type: String,
			default: this.$store.getters['auth/authHeaders'].Authorization
		}
	},
	data() {
		return {
			datatable: null,
		}
	},
	mounted() {
		var that = this;
		this.datatable = $(this.$el).DataTable({
		    "paging": this.paging,
		    "lengthChange": this.lengthChange,
		    "searching": this.searching,
		    "ordering": this.ordering,
		    "info": true,
		    "responsive": true,
		    "processing": true,
			"serverSide": this.serverSide,
			"order": this.order,
		    ajax: {
		        'url': this.url,
		        'type' : this.type,
		        "beforeSend": (xhr) => {
		            xhr.setRequestHeader("Authorization", this.authorization)
		         }
		    },
	      	"columns": this.columns,
	      	"drawCallback":function(setting){
		        $('td [data-g-action]').click(function(e){
		            e.preventDefault();
		            
		            var action = $(this).attr('data-g-action');
		            
		            var actionData = $(this).attr('data-g-actiondata');
		            
		            var args = {
		            	action: action,
		            	data: actionData
		            }

					that.$emit('gaction', args)
		        });
		    },
	    });
	},
	methods: {
		reload(url) {
			this.datatable.ajax.url(url).load();
		},
		draw() {
			this.datatable.draw();
		}
	}
}
</script>