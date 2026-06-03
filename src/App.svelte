<script>
	import { onMount } from "svelte";

	const trns = {
		"": {
			name: "English",
			print: "Print",
			empty: "Empty",
			share: "Share",
			copied: "Copied!",
			uplogo: "Upload Logo",
			addrow: "Add Line item",
			fontsrc:
				"https://fonts.googleapis.com/css2?family=Inter:ital,opsz,wght@0,14..32,100..900;1,14..32,100..900&display=swap",
			fontstyle: '"Inter", sans-serif',
		},
		th: {
			name: "ไทย",
			print: "พิมพ์",
			empty: "ล้าง",
			share: "แชร์",
			copied: "คัดลอกแล้ว!",
			uplogo: "เลือกโลโก้",
			addrow: "เพิ่มบรรทัด",
			fontsrc:
				"https://fonts.googleapis.com/css2?family=Noto+Serif+Thai:wght@100..900&display=swap",
			fontstyle: '"Noto Serif Thai", serif',
		},
	};
	const lbs = {
		"": {
			no: "No",
			date: "Date",
			dueDate: "Due date",
			payMethod: "Payment",
			vat: "Value-added tax",
			wht: "Withholding tax",
			adjust: "Adjust",
			client: "Bill to",
			clientid: "Client iD",
			clientAddress: "Client address",
			vendorLogo: "Vendor Logo",
			vendor: "Vendor name",
			vendorid: "Vendor iD",
			vendorAddress: "Vendor address",
			note: "Note",
			subtotal: "Subtotal",
			total: "Total",
			clientSign: "Client signature",
			vendorSign: "Vendor signature",
			desc: "Description",
			price: "Price",
			qty: "Qty",
			unit: "Unit",
			amount: "Amount",
			currency: "Currency",
		},
		invoice: {},
		"Tax invoice": {},
		Receipt: {
			date: "Received date",
			client: "Received from",
			total: "Paid total",
			vendorSign: "Receiver signature",
		},
		th: {
			no: "เลขที่",
			date: "วันที่",
			dueDate: "วันที่ครบกำหนด",
			payMethod: "จ่ายแบบ",
			vat: "ภาษีมูลค่าเพิ่ม",
			wht: "ภาษีหัก ณ ที่จ่าย",
			adjust: "ปรับปรุง",
			client: "ถึง",
			clientid: "เลขประจำตัวภาษีผู้ซื้อ",
			clientAddress: "ที่อยู่ผู้ซื้อ",
			vendorLogo: "โลโก้ผู้ขาย",
			vendor: "ชื่อผู้ขาย",
			vendorid: "เลขประจำตัวภาษีผู้ขาย",
			vendorAddress: "ที่อยู่ผู้ขาย",
			note: "หมายเหตุ",
			subtotal: "รวม",
			total: "รวมทั้งสิ้น",
			clientSign: "ลายเซ็นผู้ซื้อ",
			vendorSign: "ลายเซ็นผู้ขาย",
			desc: "รายละเอียด",
			price: "ราคา",
			qty: "ปริมาณ",
			unit: "หน่วยนับ",
			amount: "จำนวนเงิน",
			currency: "สกุลเงิน",
		},
		ใบแจ้งหนี้: {},
		ใบกำกับภาษี: {},
		ใบส่งของ: {
			clientSign: "ลายเซ็นผู้รับของ",
		},
		ใบเสร็จรับเงิน: {
			date: "วันที่รับเงิน",
			client: "รับเงินจาก",
			total: "จ่ายไปทั้งสิ้น",
			vendorSign: "ลายเซ็นผู้รับเงิน",
		},
		บิลเงินสด: {
			vendorSign: "ลายเซ็นผู้รับเงิน",
		},
		ใบเสนอราคา: {},
	};

	const org = {
		lang: "",
		doc: "",
		no: "",
		date: rawDate(),
		dueDate: rawDate(),
		payMethod: "",
		vendor: "",
		vendorid: "",
		vendorAddress: "",
		client: "",
		clientid: "",
		clientAddress: "",
		desc: ["", "", "", ""],
		price: ["", "", "", ""],
		qty: ["", "", "", ""],
		vatRate: "0.07",
		whtRate: "0.00",
		adjust: "",
		note: "",
		vendorLogo: "",
		currency: "",
	};

	let qry = $state({ ...org });
	let sharedUrl = $state("");

	let txt = $derived.by(() => {
		let trn = trns[""];
		if (trns[qry.lang]) {
			trn = { ...trn, ...trns[qry.lang] };
		}
		return trn;
	});
	let tag = $derived.by(() => {
		let lb = lbs[""];
		if (lbs[qry.lang]) {
			lb = { ...lb, ...lbs[qry.lang] };
		}
		if (lbs[qry.doc]) {
			lb = { ...lb, ...lbs[qry.doc] };
		}
		return lb;
	});

	let amount = $derived.by(() => {
		let amount = 0;
		qry.qty.forEach((qty, index) => {
			amount += Number(qty) * Number(qry.price[index]);
		});
		return amount;
	});

	let vatAmount = $derived(amount * Number(qry.vatRate));
	let whtAmount = $derived(amount * Number(qry.whtRate));
	let totalAmount = $derived(
		amount + vatAmount + whtAmount + (Number(qry.adjust) || 0),
	);

	function formatNumber(value, option = {}) {
		option = {
			minimumFractionDigits: 2,
			maximumFractionDigits: 2,
			...option,
		};
		value = Number(value);
		if (value == 0) {
			return "";
		} else {
			try {
				return value.toLocaleString(qry.lang, option);
			} catch {
				return value.toLocaleString(undefined, option);
			}
		}
	}
	function formatDate(value, option = {}) {
		option = {
			day: "numeric",
			month: "short",
			year: "numeric",
			...option,
		};
		try {
			return new Date(value).toLocaleDateString(qry.lang, option);
		} catch {
			return new Date(value).toLocaleDateString(undefined, option);
		}
	}
	function rawDate(value = new Date()) {
		return new Date(value).toISOString().split("T")[0];
	}

	function uploadLogo(e) {
		const file = e.target.files[0];
		if (file) {
			const reader = new FileReader();
			reader.addEventListener("load", () => {
				qry.vendorLogo = reader.result;
			});
			reader.readAsDataURL(file);
		} else {
			qry.vendorLogo = "";
		}
	}
	function addRow() {
		qry.price.push("");
		qry.qty.push("");
		qry.desc.push("");
	}
	function deleteRow(index) {
		qry.price.splice(index, 1);
		qry.qty.splice(index, 1);
		qry.desc.splice(index, 1);
	}
	function clearForm() {
		qry = { ...org, lang: qry.lang, doc: qry.doc, currency: qry.currency };
		sharedUrl = "";
	}
	let showCopied = $state(false);
	let copiedTimeout;

	function copyLink() {
		const searchParams = new URLSearchParams();
		let vendorLogo = "";
		if (qry.vendorLogo.length > 100) {
			vendorLogo = qry.vendorLogo;
			qry.vendorLogo = "";
		}
		Object.entries(qry).forEach(([key, value]) => {
			if (Array.isArray(value)) {
				value.forEach((v, i) => {
					if (v !== undefined && v !== null) {
						searchParams.append(key + "-" + i, v);
					}
				});
			} else {
				if (value !== undefined && value !== null) {
					searchParams.append(key, value);
				}
			}
		});
		qry.vendorLogo = vendorLogo;
		sharedUrl =
			window.location.origin +
			window.location.pathname +
			"?" +
			searchParams.toString();
		navigator.clipboard
			.writeText(sharedUrl)
			.then(() => {
				showCopied = true;
				clearTimeout(copiedTimeout);
				copiedTimeout = setTimeout(() => {
					showCopied = false;
				}, 3000);
			})
			.catch((err) => {
				console.error("Failed to copy link: ", err);
			});
	}
	onMount(() => {
		let userLang = navigator.language;
		if (trns[userLang]) {
			qry.lang = userLang;
		}
		const searchParams = new URLSearchParams(location.search);
		searchParams.entries().forEach(([key, value]) => {
			const [k, i] = key.split("-");
			if (i) {
				qry[k][+i] = value;
			} else if (key in org) {
				qry[key] = value;
			}
		});

		// Default currency based on initial/parsed lang
		if (qry.currency === "") {
			if (qry.lang === "th") {
				qry.currency = "฿";
			} else {
				qry.currency = "$";
			}
		}

		if (qry.lang == "") {
			qry.doc = "invoice";
		} else if (qry.lang == "th") {
			qry.doc = "ใบแจ้งหนี้";
		}
	});
</script>

<svelte:head>
	<link href={txt.fontsrc} rel="stylesheet" />
</svelte:head>

<datalist id="docs">
	{#each Object.keys(lbs) as type}
		<option>{type}</option>
	{/each}
</datalist>

<div
	class="max-w-3xl mx-auto my-4 p-4 flex justify-center print:hidden flex-wrap items-center gap-3 w-full md:w-auto"
>
	<!-- Language Switcher -->
	<div class="relative flex items-center">
		<!-- https://heroicons.com/outline language -->
		<svg
			xmlns="http://www.w3.org/2000/svg"
			fill="none"
			viewBox="0 0 24 24"
			stroke-width="2"
			stroke="currentColor"
			class="w-4 h-4 text-neutral-500 absolute left-3 pointer-events-none"
		>
			<path
				stroke-linecap="round"
				stroke-linejoin="round"
				d="m10.5 21 5.25-11.25L21 21m-9-3h7.5M3 5.621a48.474 48.474 0 0 1 6-.371m0 0c1.12 0 2.233.038 3.334.114M9 5.25V3m3.334 2.364C11.176 10.658 7.69 15.08 3 17.502m9.334-12.138c.896.061 1.785.147 2.666.257m-4.589 8.495a18.023 18.023 0 0 1-3.827-5.802"
			/>
		</svg>

		<select
			class="pl-9 pr-8 py-2 text-sm font-semibold text-neutral-600 hover:text-neutral-800 bg-neutral-50 hover:bg-neutral-100 border border-neutral-200 rounded-xl transition-all cursor-pointer appearance-none outline-none focus:ring-1 focus:ring-cyan-500 focus:border-cyan-500 field-sizing-content"
			bind:value={qry.lang}
			onchange={() => {
				if (qry.lang == "") {
					qry.doc = "invoice";
				} else if (qry.lang == "th") {
					qry.doc = "ใบแจ้งหนี้";
				}
			}}
		>
			{#each Object.keys(trns) as locale}
				<option value={locale}>
					{trns[locale]["name"]}
				</option>
			{/each}
		</select>
		<svg
			xmlns="http://www.w3.org/2000/svg"
			fill="none"
			viewBox="0 0 24 24"
			stroke-width="2.5"
			stroke="currentColor"
			class="w-3.5 h-3.5 text-neutral-400 absolute right-2.5 pointer-events-none"
		>
			<path
				stroke-linecap="round"
				stroke-linejoin="round"
				d="m19.5 8.25-7.5 7.5-7.5-7.5"
			/>
		</svg>
	</div>

	<!-- Actions -->
	<button
		class="flex items-center gap-1.5 px-3.5 py-2 text-sm font-semibold text-neutral-600 hover:text-neutral-800 bg-neutral-50 hover:bg-neutral-100 border border-neutral-200 rounded-xl transition-all cursor-pointer active:scale-95"
		onclick={() => {
			clearForm();
		}}
	>
		<svg
			xmlns="http://www.w3.org/2000/svg"
			fill="none"
			viewBox="0 0 24 24"
			stroke-width="2"
			stroke="currentColor"
			class="w-4 h-4"
		>
			<path
				stroke-linecap="round"
				stroke-linejoin="round"
				d="m14.74 9-.346 9m-4.788 0L9.26 9m9.968-3.21c.342.052.682.107 1.022.166m-1.022-.165L18.16 19.673a2.25 2.25 0 0 1-2.244 2.077H8.084a2.25 2.25 0 0 1-2.244-2.077L4.772 5.79m14.456 0a48.108 48.108 0 0 0-3.478-.397m-12 .562c.34-.059.68-.114 1.022-.165m0 0a48.11 48.11 0 0 1 3.478-.397m7.5 0v-.916c0-1.18-.91-2.164-2.09-2.201a51.964 51.964 0 0 0-3.32 0c-1.18.037-2.09 1.022-2.09 2.201v.916m7.5 0a48.667 48.667 0 0 0-7.5 0"
			/>
		</svg>
		<span>{txt.empty}</span>
	</button>

	<button
		class="flex items-center gap-1.5 px-3.5 py-2 text-sm font-semibold text-neutral-600 hover:text-neutral-800 bg-neutral-50 hover:bg-neutral-100 border border-neutral-200 rounded-xl transition-all cursor-pointer active:scale-95"
		onclick={() => {
			print();
		}}
	>
		<svg
			xmlns="http://www.w3.org/2000/svg"
			fill="none"
			viewBox="0 0 24 24"
			stroke-width="2"
			stroke="currentColor"
			class="w-4 h-4"
		>
			<path
				stroke-linecap="round"
				stroke-linejoin="round"
				d="M6.72 13.829c-.24.03-.48.062-.72.096m.72-.096a42.415 42.415 0 0 1 10.56 0m-10.56 0L6.34 18m10.94-4.171c.24.03.48.062.72.096m-.72-.096L17.66 18m0 0 .229 2.523a1.125 1.125 0 0 1-1.12 1.227H7.231c-.617 0-1.11-.497-1.12-1.115L6 18m12 0H6m12 0a1.8 1.8 0 0 0 1.8-1.8V10.5a1.8 1.8 0 0 0-1.8-1.8H4.2A1.8 1.8 0 0 0 2.4 10.5v5.7a1.8 1.8 0 0 0 1.8 1.8M15 9V3.75A1.8 1.8 0 0 0 13.2 2H10.8A1.8 1.8 0 0 0 9 3.75V9m6 0H9"
			/>
		</svg>
		<span>{txt.print}</span>
	</button>

	<!-- Share / Copy Link Button -->
	<button
		class="flex items-center gap-1.5 px-4 py-2 text-sm font-semibold text-white bg-gradient-to-r {showCopied
			? 'from-emerald-500 to-teal-600 shadow-emerald-500/20'
			: 'from-cyan-500 to-blue-600 shadow-blue-500/20'} rounded-xl shadow-md hover:opacity-95 active:scale-95 transition-all cursor-pointer"
		onclick={copyLink}
	>
		{#if showCopied}
			<svg
				xmlns="http://www.w3.org/2000/svg"
				fill="none"
				viewBox="0 0 24 24"
				stroke-width="2.5"
				stroke="currentColor"
				class="w-4 h-4 text-emerald-100"
			>
				<path
					stroke-linecap="round"
					stroke-linejoin="round"
					d="m4.5 12.75 6 6 9-13.5"
				/>
			</svg>
			<span>{txt.copied}</span>
		{:else}
			<svg
				xmlns="http://www.w3.org/2000/svg"
				fill="none"
				viewBox="0 0 24 24"
				stroke-width="2"
				stroke="currentColor"
				class="w-4 h-4"
			>
				<path
					stroke-linecap="round"
					stroke-linejoin="round"
					d="M13.19 8.688a4.5 4.5 0 0 1 1.242 7.244l-4.5 4.5a4.5 4.5 0 0 1-6.364-6.364l1.757-1.757m13.35-.622 1.757-1.757a4.5 4.5 0 0 0-6.364-6.364l-4.5 4.5a4.5 4.5 0 0 0 1.242 7.244"
				/>
			</svg>
			<span>{txt.share}</span>
		{/if}
	</button>
</div>

<div
	class="w-fit p-12 mx-auto bg-white rounded-3xl border border-neutral-100 shadow-xl shadow-neutral-100/40 print:p-0 print:shadow-none print:border-none print:rounded-none"
	style="font-family: {txt.fontstyle};"
>
	<!-- Header Section -->
	<div class="flex flex-wrap justify-between items-start gap-8 mb-12">
		<div class="">
			<!-- Left: Logo & Vendor Details -->
			<div class="flex flex-wrap items-start gap-6">
				<!-- Logo upload box -->
				{#if !qry.vendorLogo}
					<label
						class="flex flex-col items-center justify-center w-40 h-24 border-2 border-dashed border-neutral-200 hover:border-cyan-500 rounded-2xl bg-neutral-50/50 hover:bg-cyan-50/20 cursor-pointer transition-all duration-200 group print:hidden"
					>
						<input type="file" class="hidden" onchange={uploadLogo} />
						<svg
							xmlns="http://www.w3.org/2000/svg"
							fill="none"
							viewBox="0 0 24 24"
							stroke-width="1.5"
							stroke="currentColor"
							class="w-6 h-6 text-neutral-400 group-hover:text-cyan-600 transition-colors"
						>
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								d="M2.25 15.75l5.159-5.159a2.25 2.25 0 013.182 0l5.159 5.159m-1.5-1.5l1.409-1.409a2.25 2.25 0 013.182 0l2.909 2.909m-18 3.75h16.5a1.5 1.5 0 001.5-1.5V6a1.5 1.5 0 00-1.5-1.5H3.75A1.5 1.5 0 002.25 6v12a1.5 1.5 0 001.5 1.5zm10.5-11.25h.008v.008h-.008V8.25zm.375 0a.375.375 0 11-.75 0 .375.375 0 01.75 0z"
							/>
						</svg>
						<span
							class="text-xs text-neutral-400 font-semibold mt-1 group-hover:text-cyan-600 transition-colors"
							>{txt.uplogo}</span
						>
					</label>
				{:else}
					<div class="relative group w-auto h-auto inline-block">
						<img
							class="max-h-20 max-w-[200px] object-contain rounded-lg"
							src={qry.vendorLogo}
							alt={tag.vendorLogo}
						/>
						<label
							class="absolute inset-0 bg-black/40 text-white rounded-lg flex items-center justify-center opacity-0 group-hover:opacity-100 transition-all cursor-pointer print:hidden text-xs font-semibold"
						>
							<input type="file" class="hidden" onchange={uploadLogo} />
							Change Logo
						</label>
						<!-- svelte-ignore a11y_consider_explicit_label -->
						<button
							class="absolute -top-2 -right-2 bg-red-100 text-red-600 p-1 rounded-full hover:bg-red-200 transition-colors border border-red-200 print:hidden cursor-pointer"
							onclick={() => (qry.vendorLogo = "")}
						>
							<svg
								xmlns="http://www.w3.org/2000/svg"
								fill="none"
								viewBox="0 0 24 24"
								stroke-width="2.5"
								stroke="currentColor"
								class="w-3 h-3"
							>
								<path
									stroke-linecap="round"
									stroke-linejoin="round"
									d="M6 18L18 6M6 6l12 12"
								/>
							</svg>
						</button>
					</div>
				{/if}

				<!-- Vendor details inputs -->
				<div class="flex flex-col gap-1 w-full md:w-64">
					<input
						class="font-bold text-xl text-neutral-800 placeholder-neutral-300 bg-transparent hover:bg-neutral-50 focus:bg-white border-0 focus:ring-1 focus:ring-cyan-500 rounded px-2 py-0.5 -mx-2 transition-all outline-none w-full"
						type="text"
						placeholder={tag.vendor}
						bind:value={qry.vendor}
					/>
					<input
						class="text-xs font-semibold text-neutral-500 placeholder-neutral-300 bg-transparent hover:bg-neutral-50 focus:bg-white border-0 focus:ring-1 focus:ring-cyan-500 rounded px-2 py-0.5 -mx-2 transition-all outline-none w-full"
						type="text"
						placeholder={tag.vendorid}
						bind:value={qry.vendorid}
					/>
					<textarea
						class="text-sm text-neutral-500 placeholder-neutral-300 bg-transparent hover:bg-neutral-50 focus:bg-white border-0 focus:ring-1 focus:ring-cyan-500 rounded px-2 py-1 -mx-2 transition-all outline-none resize-none overflow-hidden h-auto w-full field-sizing-content"
						placeholder={tag.vendorAddress}
						bind:value={qry.vendorAddress}
					></textarea>
				</div>
			</div>

			<!-- Billing Details (Bill To) -->
			<div
				class="p-6 mt-8 bg-neutral-50/50 hover:bg-neutral-50/80 rounded-2xl border border-neutral-100/70 transition-colors"
			>
				<h3 class="text-xs font-bold text-neutral-400 tracking-wider mb-2">
					{tag.client}
				</h3>
				<div class="flex flex-col gap-1">
					<input
						class="font-bold text-neutral-800 placeholder-neutral-300 bg-transparent hover:bg-neutral-100/50 focus:bg-white border-0 focus:ring-1 focus:ring-cyan-500 rounded px-2 py-0.5 -mx-2 transition-all outline-none w-full"
						type="text"
						placeholder="Client name"
						bind:value={qry.client}
					/>
					<input
						class="text-xs font-medium text-neutral-500 placeholder-neutral-300 bg-transparent hover:bg-neutral-100/50 focus:bg-white border-0 focus:ring-1 focus:ring-cyan-500 rounded px-2 py-0.5 -mx-2 transition-all outline-none w-full"
						type="text"
						placeholder={tag.clientid}
						bind:value={qry.clientid}
					/>
					<textarea
						class="text-sm text-neutral-600 placeholder-neutral-300 bg-transparent hover:bg-neutral-100/50 focus:bg-white border-0 focus:ring-1 focus:ring-cyan-500 rounded px-2 py-1 -mx-2 transition-all outline-none resize-none overflow-hidden h-auto w-full field-sizing-content"
						placeholder={tag.clientAddress}
						bind:value={qry.clientAddress}
					></textarea>
				</div>
			</div>
		</div>

		<!-- Right: Invoice Title and Metadata -->
		<div class="flex flex-col items-start md:items-end gap-3">
			<input
				class="text-3xl font-extrabold tracking-tight text-neutral-900 bg-transparent hover:bg-neutral-50 focus:bg-white border-0 focus:ring-1 focus:ring-cyan-500 rounded px-2 py-0.5 -mx-2 transition-all outline-none w-full md:text-right"
				type="text"
				list="docs"
				placeholder="INVOICE"
				bind:value={qry.doc}
			/>

			<div class="flex flex-col gap-1 w-full md:w-60">
				<!-- Invoice No -->
				<div
					class="flex justify-between items-center text-sm border-b border-neutral-100 pb-1"
				>
					<span class="text-neutral-400 font-semibold text-xs tracking-wider"
						>{tag.no}</span
					>
					<input
						class="font-semibold text-neutral-800 text-right bg-transparent hover:bg-neutral-50 focus:bg-white border-0 focus:ring-1 focus:ring-cyan-500 rounded px-1.5 py-0.5 transition-all outline-none w-32"
						type="text"
						bind:value={qry.no}
					/>
				</div>
				<!-- Date -->
				<div
					class="flex justify-between items-center text-sm border-b border-neutral-100 pb-1"
				>
					<span class="text-neutral-400 font-semibold text-xs tracking-wider"
						>{tag.date}</span
					>
					<div
						class="grow text-right hidden print:block font-medium text-neutral-800"
					>
						{formatDate(qry.date)}
					</div>
					<input
						class="text-neutral-800 text-right bg-transparent hover:bg-neutral-50 focus:bg-white border-0 focus:ring-1 focus:ring-cyan-500 rounded px-1.5 py-0.5 transition-all outline-none w-36 print:hidden"
						type="date"
						bind:value={qry.date}
					/>
				</div>
				<!-- Due Date -->
				<div
					class="flex justify-between items-center text-sm border-b border-neutral-100 pb-1 {qry.dueDate
						? ''
						: 'print:hidden opacity-50'}"
				>
					<label class="flex items-center gap-1 cursor-pointer">
						<input
							class="print:hidden accent-cyan-500"
							type="checkbox"
							checked={Boolean(qry.dueDate)}
							onchange={(e) => {
								let check = e.currentTarget.checked;
								if (check) {
									qry.dueDate = rawDate();
								} else {
									qry.dueDate = "";
								}
							}}
						/>
						<span class="text-neutral-400 font-semibold text-xs tracking-wider"
							>{tag.dueDate}</span
						>
					</label>
					<div
						class="grow text-right hidden print:block font-medium text-neutral-800"
					>
						{formatDate(qry.dueDate)}
					</div>
					{#if qry.dueDate}
						<input
							class="text-neutral-800 text-right bg-transparent hover:bg-neutral-50 focus:bg-white border-0 focus:ring-1 focus:ring-cyan-500 rounded px-1.5 py-0.5 transition-all outline-none w-36 print:hidden"
							type="date"
							bind:value={qry.dueDate}
						/>
					{:else}
						<span
							class="text-neutral-300 text-xs italic font-medium print:hidden"
							>Not Set</span
						>
					{/if}
				</div>
				<!-- Payment Method -->
				<div class="flex justify-between items-start text-sm pt-1">
					<span
						class="text-neutral-400 font-semibold text-xs tracking-wider pt-0.5"
						>{tag.payMethod}</span
					>
					<textarea
						class="text-neutral-800 text-right bg-transparent hover:bg-neutral-50 focus:bg-white border-0 focus:ring-1 focus:ring-cyan-500 rounded px-1.5 py-0.5 transition-all outline-none resize-none overflow-hidden w-36 field-sizing-content"
						bind:value={qry.payMethod}
					></textarea>
				</div>
				<!-- Currency (Hidden in Print) -->
				<div
					class="flex justify-between items-center text-sm border-t border-dashed border-neutral-200 mt-1 pt-1 print:hidden"
				>
					<span class="text-neutral-400 font-semibold text-xs tracking-wider"
						>{tag.currency}</span
					>
					<input
						class="text-cyan-500 text-right font-bold bg-transparent hover:bg-neutral-50 focus:bg-white border-0 focus:ring-1 focus:ring-cyan-500 rounded px-1.5 py-0.5 transition-all outline-none w-16"
						type="text"
						placeholder="$, ฿, €"
						bind:value={qry.currency}
					/>
				</div>
			</div>
		</div>
	</div>

	<!-- Table Area -->
	<table class="w-full border-collapse mt-8">
		<thead>
			<tr
				class="text-xs font-bold text-neutral-400 tracking-wider border-b border-neutral-200"
			>
				<th class="pb-3 text-left font-semibold">{tag.desc}</th>
				<th class="pb-3 text-center w-24 font-semibold">{tag.qty}</th>
				<th class="pb-3 text-center w-28 font-semibold">{tag.price}</th>
				<th class="pb-3 text-right w-36 pr-6 font-semibold">{tag.amount}</th>
			</tr>
		</thead>
		<tbody>
			{#each qry.desc as _, index}
				<tr
					class="border-b border-neutral-100 hover:bg-neutral-50/30 transition-colors group"
				>
					<td class="break-all p-2 align-middle">
						<span class="hidden print:inline text-sm text-neutral-800"
							>{qry.desc[index]}</span
						>
						<input
							class="w-full bg-transparent hover:bg-neutral-100/50 focus:bg-white border-0 focus:ring-1 focus:ring-cyan-500 rounded px-2.5 py-1.5 text-sm outline-none transition-all print:hidden"
							type="text"
							placeholder="Item description"
							bind:value={qry.desc[index]}
						/>
					</td>
					<td class="text-center p-2 align-middle">
						<span class="hidden print:inline text-sm text-neutral-800">
							{formatNumber(qry.qty[index])}
						</span>
						<input
							class="w-20 text-center bg-transparent hover:bg-neutral-100/50 focus:bg-white border-0 focus:ring-1 focus:ring-cyan-500 rounded px-1.5 py-1.5 text-sm outline-none transition-all print:hidden"
							type="number"
							placeholder="0"
							bind:value={qry.qty[index]}
						/>
					</td>
					<td class="text-center p-2 align-middle">
						<span class="hidden print:inline text-sm text-neutral-800">
							{#if qry.currency}{qry.currency}
							{/if}{formatNumber(qry.price[index])}
						</span>
						<input
							class="w-24 text-center bg-transparent hover:bg-neutral-100/50 focus:bg-white border-0 focus:ring-1 focus:ring-cyan-500 rounded px-1.5 py-1.5 text-sm outline-none transition-all print:hidden"
							type="number"
							placeholder="0.00"
							bind:value={qry.price[index]}
						/>
					</td>
					<td
						class="text-right p-2 align-middle font-medium text-neutral-800 relative"
					>
						<div class="flex items-center justify-end gap-2 pr-6">
							<span>
								{#if qry.currency}{qry.currency}
								{/if}{formatNumber(
									Number(qry.qty[index]) * Number(qry.price[index]),
								)}
							</span>
						</div>
						<!-- Delete button aligned absolute-right, hidden in print, only showing opacity on row hover -->
						<!-- svelte-ignore a11y_consider_explicit_label -->
						<button
							class="absolute right-2 top-1/2 -translate-y-1/2 text-neutral-300 hover:text-red-500 cursor-pointer print:hidden opacity-0 group-hover:opacity-100 transition-opacity p-1 rounded-lg hover:bg-red-50"
							onclick={() => {
								deleteRow(index);
							}}
						>
							<svg
								xmlns="http://www.w3.org/2000/svg"
								fill="none"
								viewBox="0 0 24 24"
								stroke-width="2.5"
								stroke="currentColor"
								class="w-4 h-4"
							>
								<path
									stroke-linecap="round"
									stroke-linejoin="round"
									d="M6 18L18 6M6 6l12 12"
								/>
							</svg>
						</button>
					</td>
				</tr>
			{/each}
		</tbody>
		<tfoot>
			<tr class="border-t border-neutral-200">
				<td class="p-2 print:hidden"></td>
				<td
					class="p-4 text-right text-sm font-bold text-neutral-400 tracking-wider"
					colspan="2"
				>
					{tag.subtotal}
				</td>
				<td class="text-right p-4 font-bold text-neutral-800 pr-8">
					{#if qry.currency}{qry.currency}
					{/if}{formatNumber(amount)}
				</td>
			</tr>
		</tfoot>
	</table>

	<!-- Add Line Item Button (Dashed block) -->
	<button
		onclick={addRow}
		class="flex items-center justify-center gap-2 w-full py-3 mt-4 border border-dashed border-neutral-200 hover:border-cyan-500 rounded-xl text-sm font-semibold text-neutral-500 hover:text-cyan-600 bg-white hover:bg-cyan-50/10 active:scale-[0.99] transition-all cursor-pointer print:hidden"
	>
		<svg
			xmlns="http://www.w3.org/2000/svg"
			fill="none"
			viewBox="0 0 24 24"
			stroke-width="2.5"
			stroke="currentColor"
			class="w-4 h-4"
		>
			<path
				stroke-linecap="round"
				stroke-linejoin="round"
				d="M12 4.5v15m7.5-7.5h-15"
			/>
		</svg>
		<span>{txt.addrow}</span>
	</button>

	<!-- Footer Notes & Calculations Breakdown -->
	<div
		class="grid grid-cols-1 md:grid-cols-2 gap-8 mt-12 pt-8 border-t border-neutral-100"
	>
		<!-- Left: Notes & Conditions -->
		<div class="flex flex-col gap-2">
			<label
				for="invoice-note"
				class="text-xs font-bold text-neutral-400 tracking-wider"
				>{tag.note}</label
			>
			<textarea
				id="invoice-note"
				class="w-full text-sm text-neutral-600 bg-transparent hover:bg-neutral-50 focus:bg-white border-0 hover:border hover:border-neutral-100 focus:ring-1 focus:ring-cyan-500 rounded-xl p-3 transition-all outline-none resize-none overflow-hidden h-auto field-sizing-content"
				placeholder="Add notes or payment terms..."
				bind:value={qry.note}
			></textarea>
		</div>

		<!-- Right: Totals Breakdown -->
		<div class="flex flex-col gap-2.5">
			<!-- VAT -->
			<div class="flex justify-between items-center text-sm">
				<div class="flex items-center gap-1.5">
					<span class="text-neutral-500 font-medium">{tag.vat}</span>
					<div class="">
						<input
							class="bg-neutral-50 hover:bg-neutral-100 focus:bg-white text-center text-neutral-700 border border-neutral-200 rounded px-1.5 py-0.5 text-xs outline-none field-sizing-content transition-all font-semibold"
							type="number"
							step="0.01"
							oninput={(e) => {
								qry.vatRate = Number(e.currentTarget.value) / 100;
							}}
						/>
						<span class="font-semibold text-neutral-700">%</span>
					</div>
				</div>
				<div class="text-right font-semibold text-neutral-800">
					{#if qry.currency}{qry.currency}
					{/if}{formatNumber(vatAmount)}
				</div>
			</div>

			<!-- WHT -->
			<div
				class="flex justify-between items-center text-sm {Number(qry.whtRate)
					? ''
					: 'print:hidden opacity-50'}"
			>
				<div class="flex items-center gap-1.5">
					<label
						class="flex items-center gap-1 cursor-pointer select-none print:hidden"
					>
						<input
							class="accent-cyan-500"
							type="checkbox"
							checked={Number(qry.whtRate) > 0}
							onchange={(e) => {
								let check = e.currentTarget.checked;
								if (check) {
									qry.whtRate = "0.01";
								} else {
									qry.whtRate = "0.00";
								}
							}}
						/>
						<span class="text-neutral-500 font-medium">{tag.wht}</span>
					</label>
					<div class="">
						<input
							class="bg-neutral-50 hover:bg-neutral-100 focus:bg-white text-center text-neutral-700 border border-neutral-200 rounded px-1.5 py-0.5 text-xs outline-none field-sizing-content transition-all font-semibold"
							type="number"
							step="0.01"
							oninput={(e) => {
								qry.whtRate = Number(e.currentTarget.value) / 100;
							}}
						/>
						<span class="font-semibold text-neutral-700">%</span>
					</div>
				</div>
				<div class="text-right font-semibold text-neutral-800">
					{#if qry.currency}{qry.currency}
					{/if}{formatNumber(whtAmount)}
				</div>
			</div>

			<!-- Adjustment -->
			<div class="flex justify-between items-center text-sm">
				<span class="text-neutral-500 font-medium">{tag.adjust}</span>
				<div
					class="text-right font-semibold text-neutral-800 hidden print:block"
				>
					{#if qry.currency}{qry.currency}
					{/if}{formatNumber(qry.adjust)}
				</div>
				<input
					class="bg-neutral-50 hover:bg-neutral-100 focus:bg-white text-right font-medium border border-neutral-200 rounded px-2 py-0.5 text-xs outline-none w-24 print:hidden transition-all"
					type="number"
					bind:value={qry.adjust}
				/>
			</div>

			<!-- Total Due -->
			<div
				class="flex justify-between items-center font-bold text-xl rounded-2xl px-6 py-4.5 mt-3 shadow-lg shadow-neutral-900/10 text-neutral-900 print:shadow-none print:border print:border-neutral-200 font-sans"
			>
				<span class="tracking-wide text-xs text-neutral-500">{tag.total}</span>
				<span class="text-right text-2xl tracking-tight">
					{#if qry.currency}{qry.currency}
					{/if}{formatNumber(totalAmount)}
				</span>
			</div>
		</div>
	</div>

	<!-- Signatures -->
	<div
		class="flex flex-wrap justify-end gap-16 mt-16 pt-8 border-t border-neutral-100"
	>
		<div class="text-center w-52">
			<div class="h-16 flex items-end justify-center">
				<!-- Placeholder for signature -->
			</div>
			<div
				class="border-t border-neutral-300 pt-2 text-xs font-semibold text-neutral-500 tracking-wider"
			>
				{tag.vendorSign}
			</div>
		</div>
		<div class="text-center w-52">
			<div class="h-16 flex items-end justify-center">
				<!-- Placeholder for signature -->
			</div>
			<div
				class="border-t border-neutral-300 pt-2 text-xs font-semibold text-neutral-500 tracking-wider"
			>
				{tag.clientSign}
			</div>
		</div>
	</div>
</div>

<div class="print:hidden text-center px-2 py-4 lg:py-6 text-neutral-600">
	Made by Ai, <a
		class="underline"
		target="_blank"
		href="https://github.com/zummon">Teerapat Anantarattanachai</a
	><br />
	Something breaks, needs upgrade. Let me know<br />
</div>
