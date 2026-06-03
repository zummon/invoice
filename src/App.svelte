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
		zh: {
			name: "中文 (简体)",
			print: "打印",
			empty: "清空",
			share: "分享",
			copied: "已复制!",
			uplogo: "上传Logo",
			addrow: "添加项目",
			fontsrc:
				"https://fonts.googleapis.com/css2?family=Noto+Sans+SC:wght@100..900&display=swap",
			fontstyle: '"Noto Sans SC", sans-serif',
		},
		hi: {
			name: "हिन्दी",
			print: "प्रिंट करें",
			empty: "साफ़ करें",
			share: "साझा करें",
			copied: "कॉपी किया गया!",
			uplogo: "लोगो अपलोड करें",
			addrow: "मद जोड़ें",
			fontsrc:
				"https://fonts.googleapis.com/css2?family=Noto+Sans+Devanagari:wght@100..900&display=swap",
			fontstyle: '"Noto Sans Devanagari", sans-serif',
		},
	};
	const lbs = {
		"": {
			doc: "Document name",
			no: "No",
			date: "Date",
			dueDate: "Due date",
			payMethod: "Payment",
			vat: "Value-added tax",
			wht: "Withholding tax",
			adjust: "Adjust",
			sendTo: "Send to",
			client: "Client name",
			clientid: "Client iD",
			clientAddress: "Client address",
			vendorLogo: "Vendor Logo",
			vendor: "Vendor name",
			vendorid: "Vendor iD",
			vendorAddress: "Vendor address",
			itemDesc: "Product or service name",
			note: "Note",
			addnotes: "Add notes or payment terms...",
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
			sendTo: "Received from",
			total: "Paid total",
			vendorSign: "Receiver signature",
		},
		th: {
			doc: "ชื่อเอกสาร",
			no: "เลขที่",
			date: "วันที่",
			dueDate: "วันที่ครบกำหนด",
			payMethod: "จ่ายแบบ",
			vat: "ภาษีมูลค่าเพิ่ม",
			wht: "ภาษีหัก ณ ที่จ่าย",
			adjust: "ปรับปรุง",
			sendTo: "ส่งถึง",
			client: "ชื่อผู้ซื้อ",
			clientid: "เลขประจำตัวภาษีผู้ซื้อ",
			clientAddress: "ที่อยู่ผู้ซื้อ",
			vendorLogo: "โลโก้ผู้ขาย",
			vendor: "ชื่อผู้ขาย",
			vendorid: "เลขประจำตัวภาษีผู้ขาย",
			vendorAddress: "ที่อยู่ผู้ขาย",
			itemDesc: "ชื่อสินค้าหรือบริการ",
			note: "หมายเหตุ",
			addnotes: "เพิ่มหมายเหตุหรือเงื่อนไขการชำระเงิน...",
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
			sendTo: "รับเงินจาก",
			total: "จ่ายไปทั้งสิ้น",
			vendorSign: "ลายเซ็นผู้รับเงิน",
		},
		บิลเงินสด: {
			vendorSign: "ลายเซ็นผู้รับเงิน",
		},
		ใบเสนอราคา: {},
		zh: {
			doc: "文档名称",
			no: "编号",
			date: "日期",
			dueDate: "截至日期",
			payMethod: "付款方式",
			vat: "增值税",
			wht: "扣缴税",
			adjust: "调整",
			sendTo: "发送至",
			client: "客户名称",
			clientid: "客户税号",
			clientAddress: "客户地址",
			vendorLogo: "商家Logo",
			vendor: "商家名称",
			vendorid: "商家税号",
			vendorAddress: "商家地址",
			itemDesc: "商品或服务名称",
			note: "备注说明",
			addnotes: "添加备注或付款条件...",
			subtotal: "小计",
			total: "总计",
			clientSign: "客户签名",
			vendorSign: "商家签名",
			desc: "描述说明",
			price: "价格",
			qty: "数量",
			unit: "单位",
			amount: "金额",
			currency: "货币",
		},
		hi: {
			doc: "दस्तावेज़ का नाम",
			no: "संख्या",
			date: "दिनांक",
			dueDate: "नियत तारीख",
			payMethod: "भुगतान का प्रकार",
			vat: "मूल्य वर्धित कर (VAT)",
			wht: "स्रोत पर कर कटौती (WHT)",
			adjust: "समायोजन",
			sendTo: "सेवा में",
			client: "ग्राहक का नाम",
			clientid: "ग्राहक आईडी",
			clientAddress: "ग्राहक का पता",
			vendorLogo: "विक्रेता लोगो",
			vendor: "विक्रेता का नाम",
			vendorid: "विक्रेता आईडी",
			vendorAddress: "विक्रेता का पता",
			itemDesc: "उत्पाद या सेवा का नाम",
			note: "टिप्पणी",
			addnotes: "टिप्पणियाँ या भुगतान की शर्तें जोड़ें...",
			subtotal: "उपयोग",
			total: "कुल राशि",
			clientSign: "ग्राहक के हस्ताक्षर",
			vendorSign: "विक्रेता के हस्ताक्षर",
			desc: "विवरण",
			price: "मूल्य",
			qty: "मात्रा",
			unit: "इकाई",
			amount: "राशि",
			currency: "मुद्रा",
		},
		发票: {},
		送货单: {
			clientSign: "收货人签名",
		},
		收据: {
			date: "收款日期",
			sendTo: "收款自",
			total: "实收总额",
			vendorSign: "收款人签名",
		},
		चालान: {},
		रसीद: {
			date: "प्राप्ति तिथि",
			sendTo: "से प्राप्त किया",
			total: "भुगतान कुल",
			vendorSign: "प्राप्तकर्ता के हस्ताक्षर",
		},
		"डिलिवरी नोट": {
			clientSign: "प्राप्तकर्ता के हस्ताक्षर",
		},
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

	let showCopied = $state(false);
	let copiedTimeout;

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
			} else if (qry.lang === "zh") {
				qry.currency = "¥";
			} else if (qry.lang === "hi") {
				qry.currency = "₹";
			} else {
				qry.currency = "$";
			}
		}

		if (qry.lang == "") {
			qry.doc = "invoice";
		} else if (qry.lang == "th") {
			qry.doc = "ใบแจ้งหนี้";
		} else if (qry.lang == "zh") {
			qry.doc = "发票";
		} else if (qry.lang == "hi") {
			qry.doc = "चालान";
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

<div class="toolbar print-hidden">
	<!-- Language Switcher -->
	<div class="lang-switcher-wrapper">
		<!-- https://heroicons.com/outline language -->
		<svg
			xmlns="http://www.w3.org/2000/svg"
			fill="none"
			viewBox="0 0 24 24"
			stroke-width="2"
			stroke="currentColor"
			class="lang-switcher-icon"
		>
			<path
				stroke-linecap="round"
				stroke-linejoin="round"
				d="m10.5 21 5.25-11.25L21 21m-9-3h7.5M3 5.621a48.474 48.474 0 0 1 6-.371m0 0c1.12 0 2.233.038 3.334.114M9 5.25V3m3.334 2.364C11.176 10.658 7.69 15.08 3 17.502m9.334-12.138c.896.061 1.785.147 2.666.257m-4.589 8.495a18.023 18.023 0 0 1-3.827-5.802"
			/>
		</svg>

		<select
			class="lang-switcher-select"
			bind:value={qry.lang}
			onchange={() => {
				if (qry.lang == "") {
					qry.doc = "invoice";
				} else if (qry.lang == "th") {
					qry.doc = "ใบแจ้งหนี้";
				} else if (qry.lang == "zh") {
					qry.doc = "发票";
				} else if (qry.lang == "hi") {
					qry.doc = "चालान";
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
			class="lang-switcher-arrow"
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
		class="btn btn-action"
		onclick={() => {
			qry = { ...org, lang: qry.lang, doc: qry.doc, currency: qry.currency };
			sharedUrl = "";
		}}
	>
		<svg
			xmlns="http://www.w3.org/2000/svg"
			fill="none"
			viewBox="0 0 24 24"
			stroke-width="2"
			stroke="currentColor"
			class="btn-icon"
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
		class="btn btn-colorful"
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
			class="btn-icon"
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
		class="btn btn-action"
		class:copied={showCopied}
		onclick={() => {
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
		}}
	>
		{#if showCopied}
			<svg
				xmlns="http://www.w3.org/2000/svg"
				fill="none"
				viewBox="0 0 24 24"
				stroke-width="2.5"
				stroke="currentColor"
				class="btn-icon"
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
				class="btn-icon"
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

<div class="invoice-card" style="font-family: {txt.fontstyle};">
	<!-- Header Section -->
	<div class="invoice-header">
		<div class="invoice-header-left">
			<!-- Left: Logo & Vendor Details -->
			<div class="vendor-logo-wrapper">
				<!-- Logo upload box -->
				{#if !qry.vendorLogo}
					<label class="logo-uploader">
						<input type="file" class="hidden" onchange={uploadLogo} />
						<svg
							xmlns="http://www.w3.org/2000/svg"
							fill="none"
							viewBox="0 0 24 24"
							stroke-width="1.5"
							stroke="currentColor"
							class="logo-uploader-icon"
						>
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								d="M2.25 15.75l5.159-5.159a2.25 2.25 0 013.182 0l5.159 5.159m-1.5-1.5l1.409-1.409a2.25 2.25 0 013.182 0l2.909 2.909m-18 3.75h16.5a1.5 1.5 0 001.5-1.5V6a1.5 1.5 0 00-1.5-1.5H3.75A1.5 1.5 0 002.25 6v12a1.5 1.5 0 001.5 1.5zm10.5-11.25h.008v.008h-.008V8.25zm.375 0a.375.375 0 11-.75 0 .375.375 0 01.75 0z"
							/>
						</svg>
						<span class="logo-uploader-text">{txt.uplogo}</span>
					</label>
				{:else}
					<div class="logo-preview-container">
						<img
							class="logo-preview"
							src={qry.vendorLogo}
							alt={tag.vendorLogo}
						/>
						<label class="logo-preview-overlay">
							<input type="file" class="hidden" onchange={uploadLogo} />
							Change Logo
						</label>
						<!-- svelte-ignore a11y_consider_explicit_label -->
						<button
							class="btn-remove-logo"
							onclick={() => (qry.vendorLogo = "")}
						>
							<svg
								xmlns="http://www.w3.org/2000/svg"
								fill="none"
								viewBox="0 0 24 24"
								stroke-width="2.5"
								stroke="currentColor"
								class="btn-remove-logo-icon"
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
				<div class="vendor-details">
					<input
						class="input-text input-vendor-name"
						type="text"
						placeholder={tag.vendor}
						bind:value={qry.vendor}
					/>
					<input
						class="input-text input-vendor-id"
						type="text"
						placeholder={tag.vendorid}
						bind:value={qry.vendorid}
					/>
					<textarea
						class="textarea-auto textarea-vendor-address"
						placeholder={tag.vendorAddress}
						bind:value={qry.vendorAddress}
					></textarea>
				</div>
			</div>

			<!-- Billing Details (Bill To) -->
			<div class="client-details">
				<h3 class="client-title">
					{tag.sendTo}
				</h3>
				<div class="flex flex-col gap-1">
					<input
						class="input-text input-client-name"
						type="text"
						placeholder={tag.client}
						bind:value={qry.client}
					/>
					<input
						class="input-text input-client-id"
						type="text"
						placeholder={tag.clientid}
						bind:value={qry.clientid}
					/>
					<textarea
						class="textarea-auto textarea-client-address"
						placeholder={tag.clientAddress}
						bind:value={qry.clientAddress}
					></textarea>
				</div>
			</div>
		</div>

		<!-- Right: Invoice Title and Metadata -->
		<div class="invoice-meta">
			<input
				class="input-text input-doc-title"
				type="text"
				list="docs"
				placeholder={tag.doc}
				bind:value={qry.doc}
			/>

			<div class="meta-fields-group">
				<!-- Invoice No -->
				<div class="meta-row">
					<span class="meta-label">{tag.no}</span>
					<input
						class="input-text input-meta-value"
						type="text"
						bind:value={qry.no}
					/>
				</div>
				<!-- Date -->
				<div class="meta-row">
					<span class="meta-label">{tag.date}</span>
					<div
						class="grow text-right hidden print:block font-medium text-neutral-800"
					>
						{formatDate(qry.date)}
					</div>
					<input
						class="input-text input-meta-date print-hidden"
						type="date"
						bind:value={qry.date}
					/>
				</div>
				<!-- Due Date -->
				<div
					class="meta-row"
					class:print-hidden={!qry.dueDate}
					style={qry.dueDate ? "" : "opacity: 0.5;"}
				>
					<span class="meta-label">{tag.dueDate}</span>
					<div
						class="grow text-right hidden print:block font-medium text-neutral-800"
					>
						{formatDate(qry.dueDate)}
					</div>
					<input
						class="input-text input-meta-date print-hidden"
						type="date"
						bind:value={qry.dueDate}
					/>
				</div>
				<!-- Payment Method -->
				<div class="meta-row payment">
					<span class="meta-label">{tag.payMethod}</span>
					<textarea
						class="textarea-auto input-meta-payment"
						bind:value={qry.payMethod}
					></textarea>
				</div>
				<!-- Currency -->
				<div class="meta-row currency">
					<span class="meta-label">{tag.currency}</span>
					<input
						class="input-text input-meta-currency"
						type="text"
						placeholder="$, ฿, €"
						bind:value={qry.currency}
					/>
				</div>
			</div>
		</div>
	</div>

	<!-- Table Area -->
	<div class="invoice-table-wrapper">
		<table class="invoice-table">
			<thead>
				<tr>
					<th class="th-desc">{tag.desc}</th>
					<th class="th-qty">{tag.qty}</th>
					<th class="th-price">{tag.price}</th>
					<th class="th-amount">{tag.amount}</th>
				</tr>
			</thead>
			<tbody>
				{#each qry.desc as _, index}
					<tr>
						<td class="td-desc">
							<span class="hidden print:inline text-sm text-neutral-800"
								>{qry.desc[index]}</span
							>
							<input
								class="input-text input-table-desc print-hidden"
								type="text"
								placeholder={tag.itemDesc}
								bind:value={qry.desc[index]}
							/>
						</td>
						<td class="td-qty" data-label={tag.qty}>
							<span class="hidden print:inline text-sm text-neutral-800">
								{formatNumber(qry.qty[index])}
							</span>
							<input
								class="input-text input-table-qty print-hidden"
								type="number"
								placeholder="0"
								bind:value={qry.qty[index]}
							/>
						</td>
						<td class="td-price" data-label={tag.price}>
							<span class="hidden print:inline text-sm text-neutral-800">
								{formatNumber(qry.price[index])}
							</span>
							<input
								class="input-text input-table-price print-hidden"
								type="number"
								placeholder="0.00"
								bind:value={qry.price[index]}
							/>
						</td>
						<td class="td-amount" data-label={tag.amount}>
							<div class="amount-container">
								<span>
									{formatNumber(
										Number(qry.qty[index]) * Number(qry.price[index]),
									)}
								</span>
							</div>
							<!-- Delete button aligned absolute-right, hidden in print, only showing opacity on row hover -->
							<!-- svelte-ignore a11y_consider_explicit_label -->
							<button
								class="btn-delete-row print-hidden"
								onclick={() => {
									qry.price.splice(index, 1);
									qry.qty.splice(index, 1);
									qry.desc.splice(index, 1);
								}}
							>
								<svg
									xmlns="http://www.w3.org/2000/svg"
									fill="none"
									viewBox="0 0 24 24"
									stroke-width="2.5"
									stroke="currentColor"
									class="btn-delete-row-icon"
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
				<tr>
					<td class=""></td>
					<td class="tfoot-label" colspan="2">
						{tag.subtotal}
					</td>
					<td class="tfoot-value">
						{formatNumber(amount)}
					</td>
				</tr>
			</tfoot>
		</table>
	</div>

	<!-- Add Line Item Button (Dashed block) -->
	<button
		onclick={() => {
			qry.price.push("");
			qry.qty.push("");
			qry.desc.push("");
		}}
		class="btn-add-row print-hidden"
	>
		<svg
			xmlns="http://www.w3.org/2000/svg"
			fill="none"
			viewBox="0 0 24 24"
			stroke-width="2.5"
			stroke="currentColor"
			class="btn-add-row-icon"
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
	<div class="invoice-footer">
		<!-- Left: Notes & Conditions -->
		<div class="notes-section">
			<label for="invoice-note" class="notes-label">
				{tag.note}
			</label>
			<textarea
				id="invoice-note"
				class="textarea-notes"
				placeholder={tag.addnotes}
				bind:value={qry.note}
			></textarea>
		</div>

		<!-- Right: Totals Breakdown -->
		<div class="totals-section">
			<!-- VAT -->
			<div class="total-row">
				<label class="total-row-label">
					{tag.vat}
					<input
						class="input-total-rate"
						type="number"
						step="0.01"
						value={(Number(qry.vatRate) * 100).toFixed(2)}
						oninput={(e) => {
							qry.vatRate = String(Number(e.currentTarget.value) / 100);
						}}
					/>
					<span class="font-semibold">%</span>
				</label>
				<div class="total-row-value">
					{formatNumber(vatAmount)}
				</div>
			</div>

			<!-- WHT -->
			<div
				class="total-row"
				class:print-hidden={!Number(qry.whtRate)}
				style={Number(qry.whtRate) ? "" : "opacity: 0.5;"}
			>
				<label class="total-row-label">
					{tag.wht}
					<input
						class="input-total-rate"
						type="number"
						step="0.01"
						value={(Number(qry.whtRate) * 100).toFixed(2)}
						oninput={(e) => {
							qry.whtRate = String(Number(e.currentTarget.value) / 100);
						}}
					/>
					<span class="font-semibold">%</span>
				</label>
				<div class="total-row-value">
					{formatNumber(whtAmount)}
				</div>
			</div>

			<!-- Adjustment -->
			<div class="total-row">
				<span class="total-row-label">{tag.adjust}</span>
				<div class="total-row-value">
					<span class="hidden print:inline">
						{formatNumber(qry.adjust)}
					</span>
					<input
						class="input-adjust-value print-hidden"
						type="number"
						bind:value={qry.adjust}
					/>
				</div>
			</div>

			<!-- Total Due -->
			<div class="total-due-card">
				<span class="total-due-label">{tag.total}</span>
				<span class="total-due-value">
					{formatNumber(totalAmount)}
				</span>
			</div>
		</div>
	</div>

	<!-- Signatures -->
	<div class="signatures-section">
		<div class="signature-box">
			<div class="signature-line-wrapper">
				<!-- Placeholder for signature -->
			</div>
			<div class="signature-label">
				{tag.vendorSign}
			</div>
		</div>
		<div class="signature-box">
			<div class="signature-line-wrapper">
				<!-- Placeholder for signature -->
			</div>
			<div class="signature-label">
				{tag.clientSign}
			</div>
		</div>
	</div>
</div>
