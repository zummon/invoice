<script>
	import { onMount } from "svelte";
	const trns = {
		"": {
			"": "English",
			guide: "Select text you want to edit then type directly.",
			guide2: "Click the logo to upload yours",
			print: "Print",
			empty: "Empty",
			share: "Share",
		},
		th: {
			"": "ไทย",
			guide: "เลือกข้อความที่คุณต้องการแก้ไข แล้วพิมพ์โดยตรง",
			guide2: "คลิกโลโก้ เพื่ออัพโหลด โลโก้ ของคุณ",
			print: "พิมพ์",
			empty: "ล้าง",
			share: "เผยแพร่",
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
		no: "____",
		date: rawDate(),
		dueDate: rawDate(),
		payMethod: "________",
		vendor: "",
		vendorid: "",
		vendorAddress: "",
		client: "____",
		clientid: "",
		clientAddress: "",
		desc: ["", "", "", ""],
		price: ["", "", "", ""],
		qty: ["", "", "", ""],
		vatRate: "0.07",
		whtRate: "0.00",
		adjust: "",
		note: "________",
		vendorLogo: "",
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

	function formatNumber(value, option = {}) {
		value = Number(value);
		return value == 0
			? ""
			: value.toLocaleString(qry.lang || undefined, {
					minimumFractionDigits: 2,
					maximumFractionDigits: 2,
					...option,
				});
	}
	function formatDate(value, option = {}) {
		return new Date(value).toLocaleDateString(qry.lang || undefined, {
			day: "numeric",
			month: "short",
			year: "numeric",
			...option,
		});
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
		qry = { ...org, lang: qry.lang, doc: qry.doc };
		sharedUrl = "";
	}
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
					searchParams.append(key + "-" + i, v);
				});
			} else {
				searchParams.append(key, value);
			}
		});
		qry.vendorLogo = vendorLogo;
		sharedUrl =
			"https://codepen.io/zummon/full/wvLrqBe?" + searchParams.toString();
		navigator.clipboard.writeText(sharedUrl);
	}
	onMount(() => {
		const searchParams = new URLSearchParams(location.search);
		searchParams.entries().forEach(([key, value]) => {
			const [k, i] = key.split("-");
			if (i) {
				qry[k][+i] = value;
			} else if (org[key]) {
				qry[key] = value;
			}
		});

		if (qry.lang == "") {
			qry.doc = "invoice";
		} else if (qry.lang == "th") {
			qry.doc = "ใบแจ้งหนี้";
		}
	});
</script>

<datalist id="docs">
	{#each Object.keys(lbs) as type}
		<option>{type}</option>
	{/each}
</datalist>

<div class="flex flex-wrap justify-center px-2 py-4 lg:py-6 gap-4 print:hidden">
	{#each Object.keys(trns) as locale}
		<button
			class="font-semibold text-lg cursor-pointer border-b-2 {qry.lang == locale
				? 'text-neutral-500 border-transparent'
				: 'text-green-500 border-green-500'}"
			onclick={() => {
				qry.lang = locale;
				if (qry.lang == "") {
					qry.doc = "invoice";
				} else if (qry.lang == "th") {
					qry.doc = "ใบแจ้งหนี้";
				}
			}}>{trns[locale][""]}</button
		>
	{/each}
	<button
		class="font-semibold text-lg cursor-pointer border-b-2 text-cyan-500 border-cyan-500"
		title="Clear forms"
		onclick={() => {
			clearForm();
		}}
	>
		{txt.empty}
	</button>
	<button
		class="font-semibold text-lg cursor-pointer border-b-2 text-cyan-500 border-cyan-500"
		title="Copy shareable link to clipboard"
		onclick={() => {
			if (sharedUrl) {
				sharedUrl=''
			} else {
				copyLink();
			}
		}}
	>
		{txt.share}
	</button>
	<button
		class="font-semibold text-lg cursor-pointer border-b-2 text-cyan-500 border-cyan-500"
		title="Print"
		onclick={() => {
			print();
		}}
	>
		{txt.print}
	</button>
</div>

<div class="p-4 print:hidden text-center">
	<div class="truncate underline text-cyan-500">
		<a class="" target="_top" href={sharedUrl}>{sharedUrl}</a>
	</div>
	{txt.guide}<br />
	{txt.guide2}
</div>

<div
	class="max-w-3xl p-8 mx-auto bg-white shadow-md print:p-0 print:shadow-none"
>
	<div class="flex flex-wrap gap-2">
		<label class="cursor-pointer {qry.vendorLogo ? '' : 'print:hidden'}">
			<input
				class="hidden"
				type="file"
				onchange={(e) => {
					uploadLogo(e);
				}}
			/>
			<img class="max-h-20" src={qry.vendorLogo} alt={tag.vendorLogo} />
		</label>
		<div class="">
			<div class="">
				<input
					class="bg-transparent border-0 p-0 field-sizing-content"
					type="text"
					placeholder={tag.vendor}
					bind:value={qry.vendor}
				/>
			</div>
			<div class="">
				<input
					class="bg-transparent border-0 p-0 field-sizing-content"
					type="text"
					placeholder={tag.vendorid}
					bind:value={qry.vendorid}
				/>
			</div>
			<div class="">
				<textarea
					class="bg-transparent border-0 p-0 field-sizing-content resize-none overflow-hidden"
					placeholder={tag.vendorAddress}
					bind:value={qry.vendorAddress}
				></textarea>
			</div>
		</div>
	</div>

	<div class="flex flex-wrap gap-2 my-2 items-center">
		<div class="grow">
			<div class="flex gap-2">
				<div class="">{tag.client}</div>
				<div class="">
					<input
						class="bg-transparent border-0 p-0 field-sizing-content"
						type="text"
						bind:value={qry.client}
					/>
				</div>
			</div>
			<div class="">
				<input
					class="bg-transparent border-0 p-0 field-sizing-content"
					type="text"
					placeholder={tag.clientid}
					bind:value={qry.clientid}
				/>
			</div>
			<div class="">
				<textarea
					class="bg-transparent border-0 p-0 field-sizing-content resize-none overflow-hidden"
					placeholder={tag.clientAddress}
					bind:value={qry.clientAddress}
				></textarea>
			</div>
		</div>
		<div class="">
			<h2 class="text-2xl hidden print:block">{qry.doc}</h2>
			<input
				class="text-2xl bg-transparent border-0 p-0 field-sizing-content print:hidden"
				type="text"
				list="docs"
				placeholder="Title"
				bind:value={qry.doc}
			/>
			<div class="flex gap-2">
				<div class="">{tag.no}</div>
				<div class="">
					<input
						class="bg-transparent border-0 p-0 field-sizing-content"
						type="text"
						bind:value={qry.no}
					/>
				</div>
			</div>
			<div class="flex gap-2">
				<div class="">{tag.date}</div>
				<div class="grow hidden print:block">{formatDate(qry.date)}</div>
				<input
					class="bg-transparent border-0 p-0 print:hidden"
					type="date"
					bind:value={qry.date}
				/>
			</div>
			<div class="flex gap-2 {qry.dueDate ? '' : 'print:hidden opacity-50'}">
				<label>
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
					<span class="">{tag.dueDate}</span>
				</label>
				<div class="grow hidden print:block">{formatDate(qry.dueDate)}</div>
				<input
					class="bg-transparent border-0 p-0 print:hidden"
					type="date"
					bind:value={qry.dueDate}
				/>
			</div>
			<div class="">{tag.payMethod}</div>
			<textarea
				class="w-full bg-transparent border-0 p-0 field-sizing-content resize-none overflow-hidden"
				bind:value={qry.payMethod}
			></textarea>
		</div>
	</div>

	<table class="w-full">
		<thead>
			<tr class="font-medium">
				<td class="">{tag.desc}</td>
				<td class="border-x border-neutral-400 text-center">{tag.qty}</td>
				<td class="border-x border-neutral-400 text-center">{tag.price}</td>
				<td class="text-right">{tag.amount}</td>
			</tr>
		</thead>
		<tbody>
			{#each qry.desc as _, index}
				<tr class="border-y border-neutral-400">
					<td class="break-all p-1">
						<span class="hidden print:inline">{qry.desc[index]}</span>
						<input
							class="bg-transparent border-0 p-0 print:hidden w-full"
							type="text"
							bind:value={qry.desc[index]}
						/>
					</td>
					<td class="text-center p-1">
						<span class="hidden print:inline">
							{formatNumber(qry.qty[index])}
						</span>
						<input
							class="bg-transparent border-0 p-0 print:hidden w-20 text-center"
							type="number"
							bind:value={qry.qty[index]}
						/>
					</td>
					<td class="text-center p-1">
						<span class="hidden print:inline">
							{formatNumber(qry.price[index])}
						</span>
						<input
							class="bg-transparent border-0 p-0 print:hidden w-20 text-center"
							type="number"
							bind:value={qry.price[index]}
						/>
					</td>
					<td class="text-right p-1">
						&nbsp;<span class="">
							{formatNumber(Number(qry.qty[index]) * Number(qry.price[index]))}
						</span>
						<button
							class="text-red-500 cursor-pointer print:hidden"
							title="Delete this row"
							onclick={() => {
								deleteRow(index);
							}}
						>
							<!-- https://heroicons.com/micro x-mark -->
							<svg
								xmlns="http://www.w3.org/2000/svg"
								viewBox="0 0 16 16"
								fill="currentColor"
								class="size-6"
							>
								<path
									d="M5.28 4.22a.75.75 0 0 0-1.06 1.06L6.94 8l-2.72 2.72a.75.75 0 1 0 1.06 1.06L8 9.06l2.72 2.72a.75.75 0 1 0 1.06-1.06L9.06 8l2.72-2.72a.75.75 0 0 0-1.06-1.06L8 6.94 5.28 4.22Z"
								/>
							</svg>
						</button>
					</td>
				</tr>
			{/each}
		</tbody>
		<tfoot>
			<tr class="">
				<td class="">
					<button
						class="text-green-500 cursor-pointer print:hidden"
						title="Add a new row"
						onclick={() => {
							addRow();
						}}
					>
						<!-- https://heroicons.com/micro plus -->
						<svg
							xmlns="http://www.w3.org/2000/svg"
							viewBox="0 0 16 16"
							fill="currentColor"
							class="size-8"
						>
							<path
								d="M8.75 3.75a.75.75 0 0 0-1.5 0v3.5h-3.5a.75.75 0 0 0 0 1.5h3.5v3.5a.75.75 0 0 0 1.5 0v-3.5h3.5a.75.75 0 0 0 0-1.5h-3.5v-3.5Z"
							/>
						</svg>
					</button>
				</td>
				<td
					class="text-center border-r border-b border-neutral-400"
					colspan="2"
				>
					{tag.subtotal}
				</td>
				<td class="text-right border-b border-neutral-400">
					{formatNumber(amount)}
				</td>
			</tr>
		</tfoot>
	</table>

	<div class="flex flex-wrap-reverse gap-4 my-2">
		<div class="grow">
			<div>{tag.note}</div>
			<textarea
				class="w-full bg-transparent border-0 p-0 field-sizing-content resize-none overflow-hidden"
				bind:value={qry.note}
			></textarea>
		</div>
		<div class="">
			<div class="flex gap-4">
				<div class="">
					<span class="">{tag.vat}</span>
					<span class="hidden print:inline">
						{formatNumber(Number(qry.vatRate) * 100)}%
					</span>
					<input
						class="bg-transparent border-0 p-0 print:hidden w-20 text-center"
						type="number"
						step="0.01"
						bind:value={qry.vatRate}
					/>
				</div>
				<div class="grow text-right">
					{formatNumber(amount * Number(qry.vatRate))}
				</div>
			</div>
			<div class="flex gap-4 {Number(qry.whtRate) ? '' : 'print:hidden opacity-50'}">
				<div class="">
					<label>
						<input
							class="print:hidden accent-cyan-500"
							type="checkbox"
							onchange={(e) => {
								let check = e.currentTarget.checked;
								if (check) {
									qry.whtRate = "0.01";
								} else {
									qry.whtRate = "0.00";
								}
							}}
						/>
						<span class="">{tag.wht}</span>
					</label>
					<span class="hidden print:inline">
						{formatNumber(Number(qry.whtRate) * 100)}%
					</span>
					<input
						class="bg-transparent border-0 p-0 print:hidden w-20 text-center"
						type="number"
						step="0.01"
						bind:value={qry.whtRate}
					/>
				</div>
				<div class="grow text-right">
					{formatNumber(amount * Number(qry.whtRate))}
				</div>
			</div>
			<div class="flex gap-4">
				<div class="">{tag.adjust}</div>
				<div class="grow text-right hidden print:block">
					{formatNumber(qry.adjust)}
				</div>
				<input
					class="grow bg-transparent border-0 p-0 print:hidden text-right"
					type="number"
					bind:value={qry.adjust}
				/>
			</div>
			<div class="flex gap-4 font-medium">
				<div class="">{tag.total}</div>
				<div class="grow text-right">
					{formatNumber(
						amount +
							amount * Number(qry.vatRate) +
							amount * Number(qry.whtRate) +
							Number(qry.adjust),
					)}
				</div>
			</div>
		</div>
	</div>

	<div class="flex flex-wrap justify-end gap-8">
		<div class="">
			<br /><br />
			<div class="border-t border-neutral-400">{tag.vendorSign}</div>
		</div>
		<div class="">
			<br /><br />
			<div class="border-t border-neutral-400">{tag.clientSign}</div>
		</div>
	</div>
</div>
