<script lang="ts">
	import { WEBUI_BASE_URL } from '$lib/constants';
	import { WEBUI_NAME, config, user, showSidebar } from '$lib/stores';
	import { goto } from '$app/navigation';
	import { onMount, getContext } from 'svelte';

	import dayjs from 'dayjs';
	import relativeTime from 'dayjs/plugin/relativeTime';
	import localizedFormat from 'dayjs/plugin/localizedFormat';
	dayjs.extend(relativeTime);
	dayjs.extend(localizedFormat);

	import { toast } from 'svelte-sonner';

	import { LineChart } from '@carbon/charts-svelte';

	import { updateUserRole, getUsers, deleteUserById } from '$lib/apis/users';

	import Pagination from '$lib/components/common/Pagination.svelte';
	import ChatBubbles from '$lib/components/icons/ChatBubbles.svelte';
	import Tooltip from '$lib/components/common/Tooltip.svelte';

	import EditUserModal from '$lib/components/admin/Users/UserList/EditUserModal.svelte';
	import UserChatsModal from '$lib/components/admin/Users/UserList/UserChatsModal.svelte';
	import AddUserModal from '$lib/components/admin/Users/UserList/AddUserModal.svelte';

	import ConfirmDialog from '$lib/components/common/ConfirmDialog.svelte';
	import Badge from '$lib/components/common/Badge.svelte';
	import Plus from '$lib/components/icons/Plus.svelte';
	import ChevronUp from '$lib/components/icons/ChevronUp.svelte';
	import ChevronDown from '$lib/components/icons/ChevronDown.svelte';
	import About from '$lib/components/chat/Settings/About.svelte';

	const i18n = getContext('i18n');

	export let requests: { model: string; timestamp: number; requests: number }[] = [];
	export let users = [];

	let parsedRequests = requests.map((item) => {
		return {
			group: item.model,
			date: dayjs(item.timestamp).format('YYYY-MM-DD HH:mm:ss'),
			requests: item.requests
		};
	});

	let search = '';
	let selectedUser = null;
</script>

<div class="flex flex-col lg:flex-row w-full h-full pb-2 lg:space-x-4">
	{#if parsedRequests.length === 0}
		<div class="flex flex-col items-center justify-center w-full h-full">
			<div class="text-5xl">📊</div>
			<div class="text-xl font-semibold">{$i18n.t('No data available')}</div>
		</div>
	{:else}
		<div class="flex flex-col w-full lg:w-3/4">
			<LineChart
				data={parsedRequests}
				options={{
					theme: 'g90',
					title: $i18n.t('Requests within the specified time range'),
					axes: {
						left: {
							mapsTo: 'requests'
						},
						bottom: {
							scaleType: 'time',
							mapsTo: 'date'
						}
					},
					legend: {
						clickable: false
					},
					height: '400px'
				}}
			/>
		</div>
	{/if}
</div>
