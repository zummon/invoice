<script>
	import { onMount, flushSync } from "svelte";

	const lngs = {
		"": {
			doc: "Document name",
			name: "English",
			print: "Print",
			empty: "Empty",
			share: "Share",
			copied: "Copied!",
			uplogo: "Upload Logo",
			pastelogo: "Or paste image URL...",
			addrow: "Add Line item",
			fontsrc:
				"https://fonts.googleapis.com/css2?family=Inter:ital,opsz,wght@0,14..32,100..900;1,14..32,100..900&display=swap",
			fontstyle: '"Inter", sans-serif',
		},
		th: {
			doc: "ชื่อเอกสาร",
			name: "ไทย",
			print: "พิมพ์",
			empty: "ล้าง",
			share: "แชร์",
			copied: "คัดลอกแล้ว!",
			uplogo: "เลือกโลโก้",
			pastelogo: "หรือวางลิงก์รูปภาพ...",
			addrow: "เพิ่มบรรทัด",
			fontsrc:
				"https://fonts.googleapis.com/css2?family=Noto+Serif+Thai:wght@100..900&display=swap",
			fontstyle: '"Noto Serif Thai", serif',
		},
		zh: {
			doc: "文档名称",
			name: "中文 (简体)",
			print: "打印",
			empty: "清空",
			share: "分享",
			copied: "已复制!",
			uplogo: "上传Logo",
			pastelogo: "或粘贴图片链接...",
			addrow: "添加项目",
			fontsrc:
				"https://fonts.googleapis.com/css2?family=Noto+Sans+SC:wght@100..900&display=swap",
			fontstyle: '"Noto Sans SC", sans-serif',
		},
		hi: {
			doc: "दस्तावेज़ का नाम",
			name: "हिन्दी",
			print: "प्रिंट करें",
			empty: "साफ़ करें",
			share: "साझा करें",
			copied: "कॉपी किया गया!",
			uplogo: "लोगो अपलोड करें",
			pastelogo: "या छवि URL पेस्ट करें...",
			addrow: "मद जोड़ें",
			fontsrc:
				"https://fonts.googleapis.com/css2?family=Noto+Sans+Devanagari:wght@100..900&display=swap",
			fontstyle: '"Noto Sans Devanagari", sans-serif',
		},
	};
	const tmps = {
		"": {
			"": {
				name: "Invoice",
				no: "No",
				date: "Date",
				dueDate: "Due date",
				payMethod: "Payment",
				vat: "Value-added tax",
				wht: "Withholding tax",
				adjust: "Adjust",
				sendTo: "Send to",
				client: "Client name",
				clientid: "Client ID",
				clientAddress: "Client address",
				vendorLogo: "Vendor Logo",
				vendor: "Vendor name",
				vendorid: "Vendor ID",
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
			"tax-invoice": { name: "Tax invoice" },
			"delivery-note": {
				name: "Delivery note",
				clientSign: "Recipient signature",
			},
			receipt: {
				name: "Receipt",
				date: "Received date",
				sendTo: "Received from",
				total: "Paid total",
				vendorSign: "Receiver signature",
			},
			"cash-receipt": {
				name: "Cash receipt",
				vendorSign: "Receiver signature",
			},
			quotation: { name: "Quotation" },
		},
		th: {
			"": {
				name: "ใบแจ้งหนี้",
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
			"tax-invoice": { name: "ใบกำกับภาษี" },
			"delivery-note": {
				name: "ใบส่งของ",
				clientSign: "ลายเซ็นผู้รับของ",
			},
			receipt: {
				name: "ใบเสร็จรับเงิน",
				date: "วันที่รับเงิน",
				sendTo: "รับเงินจาก",
				total: "จ่ายไปทั้งสิ้น",
				vendorSign: "ลายเซ็นผู้รับเงิน",
			},
			"cash-receipt": {
				name: "บิลเงินสด",
				vendorSign: "ลายเซ็นผู้รับเงิน",
			},
			quotation: { name: "ใบเสนอราคา" },
		},
		zh: {
			"": {
				name: "发票",
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
			"tax-invoice": { name: "增值税发票" },
			"delivery-note": {
				name: "送货单",
				clientSign: "收货人签名",
			},
			receipt: {
				name: "收据",
				date: "收款日期",
				sendTo: "收款自",
				total: "实收总额",
				vendorSign: "收款人签名",
			},
			"cash-receipt": {
				name: "现金收据",
				vendorSign: "收款人签名",
			},
			quotation: { name: "报价单" },
		},
		hi: {
			"": {
				name: "चालान",
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
			"tax-invoice": { name: "कर चालान" },
			"delivery-note": {
				name: "डिलिवरी नोट",
				clientSign: "प्राप्तकर्ता के हस्ताक्षर",
			},
			receipt: {
				name: "रसीद",
				date: "प्राप्ति तिथि",
				sendTo: "से प्राप्त किया",
				total: "भुगतान कुल",
				vendorSign: "प्राप्तकर्ता के हस्ताक्षर",
			},
			"cash-receipt": {
				name: "नकद रसीद",
				vendorSign: "प्राप्तकर्ता के हस्ताक्षर",
			},
			quotation: { name: "कोटेशन" },
		},
	};
	const org = {
		lang: "",
		temp: "",
		doc: "", // document title
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
		price: [0, 0, 0, 0],
		qty: [0, 0, 0, 0],
		vatRate: 0.07,
		whtRate: 0.0,
		adjust: 0,
		note: "",
		vendorLogo: "",
		currency: "",
	};
	let copiedTimeout;

	let qry = $state({ ...org });
	let showCopied = $state(false);
	let isPrinting = $state(false);

	let lng = $derived.by(() => {
		let lng = lngs[""];
		if (lngs[qry.lang]) {
			lng = { ...lng, ...lngs[qry.lang] };
		}
		return lng;
	});
	let tmp = $derived.by(() => {
		let tmp = tmps[""][""];
		if (tmps[qry.lang] && tmps[qry.lang][""]) {
			tmp = { ...tmp, ...tmps[qry.lang][""] };
		}
		if (tmps[qry.lang] && tmps[qry.lang][qry.temp]) {
			tmp = { ...tmp, ...tmps[qry.lang][qry.temp] };
		}
		return tmp;
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

	function formatNumber(value = 0, option = {}) {
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
	function formatDate(value = "", option = {}) {
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

	function changeLanguage(value = "", prevLang = "") {
		// Default currency based on initial/parsed lang
		if (
			qry.currency === "" ||
			qry.currency === "$" ||
			qry.currency === "฿" ||
			qry.currency === "¥" ||
			qry.currency === "₹"
		) {
			if (value === "th") {
				qry.currency = "฿";
			} else if (value === "zh") {
				qry.currency = "¥";
			} else if (value === "hi") {
				qry.currency = "₹";
			} else {
				qry.currency = "$";
			}
		}

		// Find the name of the template in the previous language
		const prevTempName =
			(prevLang && tmps[prevLang] && tmps[prevLang][qry.temp]?.name) ||
			tmps[""][qry.temp]?.name ||
			"";

		// If qry.doc is empty or matches the name of the template in the previous language, update it
		if (qry.doc === "" || qry.doc === prevTempName) {
			const newTempName =
				(tmps[value] && tmps[value][qry.temp]?.name) ||
				tmps[""][qry.temp]?.name ||
				"";
			qry.doc = newTempName;
		}
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

	onMount(() => {
		let userLang = navigator.language;
		if (lngs[userLang]) {
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

		changeLanguage(qry.lang);

		const before = () => {
			flushSync(() => {
				isPrinting = true;
			});
		};
		const after = () => {
			isPrinting = false;
		};
		window.addEventListener("beforeprint", before);
		window.addEventListener("afterprint", after);

		return () => {
			window.removeEventListener("beforeprint", before);
			window.removeEventListener("afterprint", after);
		};
	});
</script>

<svelte:head>
	<link href={lng.fontsrc} rel="stylesheet" />
</svelte:head>

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
			value={qry.lang}
			onchange={(e) => {
				const prevLang = qry.lang;
				qry.lang = e.currentTarget.value;
				changeLanguage(qry.lang, prevLang);
			}}
		>
			{#each Object.keys(lngs) as key}
				<option value={key}>
					{lngs[key]["name"]}
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

	<!-- Template Selector -->
	<div class="temp-switcher-wrapper">
		<svg
			xmlns="http://www.w3.org/2000/svg"
			fill="none"
			viewBox="0 0 24 24"
			stroke-width="2"
			stroke="currentColor"
			class="temp-switcher-icon"
		>
			<path
				stroke-linecap="round"
				stroke-linejoin="round"
				d="M19.5 14.25v-2.625a3.375 3.375 0 0 0-3.375-3.375h-1.5A1.125 1.125 0 0 1 13.5 7.125v-1.5a3.375 3.375 0 0 0-3.375-3.375H8.25m2.25 0H5.625c-.621 0-1.125.504-1.125 1.125v17.25c0 .621.504 1.125 1.125 1.125h12.75c.621 0 1.125-.504 1.125-1.125V11.25a9 9 0 0 0-9-9Z"
			/>
		</svg>

		<select
			class="temp-switcher-select"
			value={qry.temp}
			onchange={(e) => {
				const prevTemp = qry.temp;
				const prevTempName =
					(tmps[qry.lang] && tmps[qry.lang][prevTemp]?.name) ||
					tmps[""][prevTemp]?.name ||
					"";
				const newTemp = e.currentTarget.value;
				const newTempName =
					(tmps[qry.lang] && tmps[qry.lang][newTemp]?.name) ||
					tmps[""][newTemp]?.name ||
					"";

				if (qry.doc === "" || qry.doc === prevTempName) {
					qry.doc = newTempName;
				}
				qry.temp = newTemp;
			}}
		>
			{#each Object.keys(tmps[qry.lang] || tmps[""]) as key}
				<option value={key}>
					{(tmps[qry.lang] &&
						tmps[qry.lang][key] &&
						tmps[qry.lang][key].name) ||
						(tmps[""][key] && tmps[""][key].name) ||
						key}
				</option>
			{/each}
		</select>
		<svg
			xmlns="http://www.w3.org/2000/svg"
			fill="none"
			viewBox="0 0 24 24"
			stroke-width="2.5"
			stroke="currentColor"
			class="temp-switcher-arrow"
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
			qry = { ...org, lang: qry.lang };
			changeLanguage(qry.lang);
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
		<span>{lng.empty}</span>
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
		<span>{lng.print}</span>
	</button>

	<!-- Share / Copy Link Button -->
	<button
		class="btn btn-action"
		class:copied={showCopied}
		onclick={() => {
			const searchParams = new URLSearchParams();
			let vendorLogo = "";
			if (qry.vendorLogo && qry.vendorLogo.startsWith("data:")) {
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
			let sharedUrl =
				"https://codepen.io/zummon/full/wvLrqBe?" + searchParams.toString();
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
			<span>{lng.copied}</span>
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
			<span>{lng.share}</span>
		{/if}
	</button>
</div>

<div class="invoice-card" style="font-family: {lng.fontstyle};">
	<!-- Header Section -->
	<!-- Header Section (2 Columns) -->
	<div class="grid grid-cols-2 gap-8 mb-8 print:mb-4">
		<!-- Column 1: Vendor & Client (stacked vertically) -->
		<div class="flex flex-col gap-6">
			<!-- Row 1: Vendor Section (No border) -->
			<div class="flex flex-wrap items-start gap-6 py-2 px-0">
				<!-- Logo upload box -->
				{#if !qry.vendorLogo}
					<div
						class="logo-uploader-group print-hidden flex flex-col gap-2 w-32"
					>
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
							<span class="logo-uploader-text">{lng.uplogo}</span>
						</label>
						<input
							type="text"
							class="input-logo-url"
							placeholder={lng.pastelogo}
							bind:value={qry.vendorLogo}
						/>
					</div>
				{:else}
					<div class="logo-preview-container">
						<img
							class="logo-preview"
							src={qry.vendorLogo}
							alt={tmp.vendorLogo}
						/>
						<label class="logo-preview-overlay">
							<input type="file" class="hidden" onchange={uploadLogo} />
							Change Logo
						</label>
						<!-- svelte-ignore a11y_consider_explicit_label -->
						<button
							class="btn-remove-logo print-hidden"
							onclick={() => {
								qry.vendorLogo = "";
							}}
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
				<div class="vendor-details flex flex-col gap-1 flex-1">
					<input
						class="input-text input-vendor-name"
						type="text"
						placeholder={tmp.vendor}
						bind:value={qry.vendor}
					/>
					<input
						class="input-text input-vendor-id"
						type="text"
						placeholder={tmp.vendorid}
						bind:value={qry.vendorid}
					/>
					<textarea
						class="textarea-auto textarea-vendor-address"
						placeholder={tmp.vendorAddress}
						bind:value={qry.vendorAddress}
					></textarea>
				</div>
			</div>

			<!-- Row 2: Client Card (shrunken, has border) -->
			<div class="p-4 bg-card rounded-xl border shadow-sm transition-all hover:border-primary hover:shadow-md w-full print:p-0 print:border-none print:shadow-none print:bg-transparent print:mt-4">
				<h3
					class="client-title text-xs font-bold text-light uppercase tracking-wider mb-1"
				>
					{tmp.sendTo}
				</h3>
				<div class="flex flex-col gap-1">
					<input
						class="input-text input-client-name"
						type="text"
						placeholder={tmp.client}
						bind:value={qry.client}
					/>
					<input
						class="input-text input-client-id"
						type="text"
						placeholder={tmp.clientid}
						bind:value={qry.clientid}
					/>
					<textarea
						class="textarea-auto textarea-client-address"
						placeholder={tmp.clientAddress}
						bind:value={qry.clientAddress}
					></textarea>
				</div>
			</div>
		</div>

		<!-- Column 2: Document Meta Card (no border, spans height of col 1) -->
		<div class="meta-card flex flex-col gap-4 items-end h-full">
			<input
				class="input-text input-doc-title"
				type="text"
				placeholder={lng.doc}
				bind:value={qry.doc}
			/>

			<div
				class="meta-fields-group flex flex-col gap-2 w-full flex-1 justify-end"
			>
				<!-- Invoice No -->
				<div class="meta-row flex justify-between items-center text-sm">
					<span class="meta-label">{tmp.no}</span>
					<input
						class="input-text input-meta-value"
						type="text"
						bind:value={qry.no}
					/>
				</div>
				<!-- Date -->
				<div class="meta-row flex justify-between items-center text-sm">
					<span class="meta-label">{tmp.date}</span>
					<input
						class="input-text input-meta-date"
						type={isPrinting ? "text" : "date"}
						value={isPrinting ? formatDate(qry.date) : qry.date}
						oninput={(e) => {
							if (!isPrinting) {
								qry.date = e.currentTarget.value;
							}
						}}
					/>
				</div>
				<!-- Due Date -->
				<div
					class="meta-row flex justify-between items-center text-sm"
					class:print-hidden={!qry.dueDate}
					style={qry.dueDate ? "" : "opacity: 0.5;"}
				>
					<span class="meta-label">{tmp.dueDate}</span>
					<input
						class="input-text input-meta-date"
						type={isPrinting ? "text" : "date"}
						value={isPrinting ? formatDate(qry.dueDate) : qry.dueDate}
						oninput={(e) => {
							if (!isPrinting) {
								qry.dueDate = e.currentTarget.value;
							}
						}}
					/>
				</div>
				<!-- Payment Method -->
				<div
					class="meta-row payment flex justify-between items-start text-sm mt-1 pt-1 border-t border-dashed border-slate-200"
				>
					<span class="meta-label">{tmp.payMethod}</span>
					<textarea
						class="textarea-auto input-meta-payment"
						bind:value={qry.payMethod}
					></textarea>
				</div>
				<!-- Currency -->
				<div
					class="meta-row currency flex justify-between items-center text-sm"
				>
					<span class="meta-label">{tmp.currency}</span>
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
					<th class="th-desc">{tmp.desc}</th>
					<th class="th-qty">{tmp.qty}</th>
					<th class="th-price">{tmp.price}</th>
					<th class="th-amount">{tmp.amount}</th>
				</tr>
			</thead>
			<tbody>
				{#each qry.desc as _, index}
					<tr>
						<td class="td-desc">
							<textarea
								class="textarea-auto input-table-desc"
								placeholder={tmp.itemDesc}
								bind:value={qry.desc[index]}
							></textarea>
						</td>
						<td class="td-qty" data-label={tmp.qty}>
							<input
								class="input-text input-table-qty"
								type={isPrinting ? "text" : "number"}
								placeholder="0"
								value={isPrinting
									? formatNumber(qry.qty[index])
									: qry.qty[index]}
								oninput={(e) => {
									if (!isPrinting) {
										qry.qty[index] = +e.currentTarget.value;
									}
								}}
							/>
						</td>
						<td class="td-price" data-label={tmp.price}>
							<input
								class="input-text input-table-price"
								type={isPrinting ? "text" : "number"}
								placeholder="0.00"
								value={isPrinting
									? formatNumber(qry.price[index])
									: qry.price[index]}
								oninput={(e) => {
									if (!isPrinting) {
										qry.price[index] = +e.currentTarget.value;
									}
								}}
							/>
						</td>
						<td class="td-amount" data-label={tmp.amount}>
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
						{tmp.subtotal}
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
			qry.price.push(0);
			qry.qty.push(0);
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
		<span>{lng.addrow}</span>
	</button>

	<!-- Footer Notes & Calculations Breakdown -->
	<div class="invoice-footer">
		<!-- Left: Notes & Conditions -->
		<div class="notes-section">
			<label for="invoice-note" class="notes-label">
				{tmp.note}
			</label>
			<textarea
				id="invoice-note"
				class="textarea-notes"
				placeholder={tmp.addnotes}
				bind:value={qry.note}
			></textarea>
		</div>

		<!-- Right: Totals Breakdown -->
		<div class="totals-section">
			<!-- VAT -->
			<div class="total-row">
				<label class="total-row-label">
					{tmp.vat}
					<input
						class="input-total-rate"
						type={isPrinting ? "text" : "number"}
						step="0.01"
						value={isPrinting
							? formatNumber(Number(qry.vatRate) * 100)
							: (Number(qry.vatRate) * 100).toFixed(2)}
						oninput={(e) => {
							if (!isPrinting) {
								qry.vatRate = Number(e.currentTarget.value) / 100;
							}
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
					{tmp.wht}
					<input
						class="input-total-rate"
						type={isPrinting ? "text" : "number"}
						step="0.01"
						value={isPrinting
							? formatNumber(Number(qry.whtRate) * 100)
							: (Number(qry.whtRate) * 100).toFixed(2)}
						oninput={(e) => {
							if (!isPrinting) {
								qry.whtRate = Number(e.currentTarget.value) / 100;
							}
						}}
					/>
					<span class="font-semibold">%</span>
				</label>
				<div class="total-row-value">
					{formatNumber(whtAmount)}
				</div>
			</div>

			<!-- Adjustment -->
			<div
				class="total-row"
				class:print-hidden={!Number(qry.adjust)}
				style={Number(qry.adjust) ? "" : "opacity: 0.5;"}
			>
				<span class="total-row-label">{tmp.adjust}</span>
				<div class="total-row-value">
					<input
						class="input-adjust-value"
						type={isPrinting ? "text" : "number"}
						value={isPrinting ? formatNumber(qry.adjust) : qry.adjust}
						oninput={(e) => {
							if (!isPrinting) {
								qry.adjust = +e.currentTarget.value;
							}
						}}
					/>
				</div>
			</div>

			<!-- Total Due -->
			<div class="total-due-card">
				<span class="total-due-label">{tmp.total}</span>
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
				{tmp.vendorSign}
			</div>
		</div>
		<div class="signature-box">
			<div class="signature-line-wrapper">
				<!-- Placeholder for signature -->
			</div>
			<div class="signature-label">
				{tmp.clientSign}
			</div>
		</div>
	</div>
</div>
