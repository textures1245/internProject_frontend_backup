<script>
	import './../app.css';
	import Sidebar from './../components/Sidebar.svelte';
	import Navbar from './../components/Navbar.svelte';
	import { page } from '$app/stores';

	/** @type {import('./$types').LayoutData} */
	export let data;

	let storeId = data.account_token ? data.account_token.account.id : 0;
	let adminId = data.account_token ? data.account_token.account.id : 0;

	/**
	 * Represents a route injection object.
	 * @typedef {Object} RouteInjection
	 * @property {string} hrefTarget - The target URL of the route.
	 * @property {string} routeName - The name of the route.
	 */

	/** @type {RouteInjection[]} */
	let sidebarRoutes = [
		// admin routes
		{ hrefTarget: `/admin/${adminId}/role-management`, routeName: 'สิทธ์ใช้งาน' },
		{ hrefTarget: `/admin/${adminId}/super-admin`, routeName: 'ผู้ดูแลระบบ' },
		{ hrefTarget: `/admin/${adminId}/store-management`, routeName: 'ผู้ขาย' },
		{ hrefTarget: `/admin/${adminId}/customer-management`, routeName: 'ลูกค้า' },
		{ hrefTarget: `/admin/${adminId}log-management`, routeName: 'Log' },

		// store routes
		{ hrefTarget: `/store/${storeId}/product-management`, routeName: 'สินค้า' },
		{ hrefTarget: `/store/${storeId}/product-category-management`, routeName: 'หมวดหมู่' },
		{ hrefTarget: `/store/${storeId}/order-management`, routeName: 'รายการธุรกรรม' },
		{ hrefTarget: `/store/${storeId}/bank-management`, routeName: 'บัญชีธนาคาร' }
	];

	if (data.account_token) {
		if (data.account_token.account.role === 'ADMIN') {
			sidebarRoutes = sidebarRoutes.slice(0, 5);
		} else {
			sidebarRoutes = sidebarRoutes.slice(5);
		}
	}

	/** @returns {boolean}    */
	/** @param {string} route   */
	const routePrevents = (route) => {
		const preventRoutes = ['/customer-cart', '/customer-order', '/product'];

		for (let i = 0; i < preventRoutes.length; i++) {
			if (route.startsWith(preventRoutes[i])) {
				return true;
			}
		}
		return false;
	};

	$: currentPath = $page.url.pathname;

	import { beforeNavigate, afterNavigate } from '$app/navigation';

	let loading = false;

	beforeNavigate((nav) => {
		loading = true;
	});
	afterNavigate(() => {
		loading = false;
	});
</script>

{#if currentPath.split('/').length > 2}
	<div style="background-color: #F8F9FA !important;" class="h ibm-plex-sans-thai-light">
		<slot name="navbar">
			<Navbar />
		</slot>
		<div class="row gap-0 m-0">
			{#if !currentPath.startsWith('/customer')}
				<slot name="sidebar">
					<div class="col p-0">
						<Sidebar routes={sidebarRoutes} />
					</div>
				</slot>
			{/if}

			<div class:col-xl-10={!currentPath.startsWith('/customer')}>
				<div class="tw-min-h-screen pt-4 bg-background">
					{#if !loading}
						<slot></slot>
					{:else}
						<div
							class="tw-flex tw-justify-center tw-text-black tw-items-center tw-h-screen tw-w-full"
						>
							<div
								style="width: 5rem; height: 5rem;"
								class="spinner-border text-primary"
								role="status"
							>
								<span class="visually-hidden">Loading...</span>
							</div>
						</div>
					{/if}
				</div>
			</div>
		</div>
		<slot name="footer"></slot>
	</div>
{:else}
	<div style="background-color: #F8F9FA !important;" class="h ibm-plex-sans-thai-light">
		<div class=" pt-4 bg-background">
			<slot></slot>
		</div>
		<slot name="footer"></slot>
	</div>
{/if}

<style>
	.h {
		min-height: 100vh;
	}

	@media (min-width: 1024px) {
		.h {
			height: 100%;
		}
	}
</style>
