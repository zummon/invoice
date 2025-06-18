<script>
  import { onMount } from "svelte";
	const trns = {
		en: {
			title: "English",
			docs: {
				"": {
					title: "invoice",
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
					amount: "Amount"
				},
				taxinvoice: {
					title: "Tax invoice"
				},
				receipt: {
					title: "Receipt",
					date: "Received date",
					client: "Received from",
					total: "Paid total",
					vendorSign: "Receiver signature"
				}
			}
		},
		th: {
			title: "ไทย",
			docs: {
				"": {
					title: "ใบแจ้งหนี้",
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
					amount: "จำนวนเงิน"
				},
				taxinvoice: {
					title: "ใบกำกับภาษี"
				},
				delivery: {
					title: "ใบส่งของ",
					clientSign: "ลายเซ็นผู้รับของ"
				},
				receipt: {
					title: "ใบเสร็จรับเงิน",
					date: "วันที่รับเงิน",
					client: "รับเงินจาก",
					total: "จ่ายไปทั้งสิ้น",
					vendorSign: "ลายเซ็นผู้รับเงิน"
				},
				cashSale: {
					title: "บิลเงินสด",
					vendorSign: "ลายเซ็นผู้รับเงิน"
				},
				quotation: {
					title: "ใบเสนอราคา",
				}
			}
		}
	}

  let currentDate = new Date()
  let nextMonthDate = new Date(currentDate)
  nextMonthDate.setMonth(currentDate.getMonth() + 1)

	const org = {
		lang: "en",
    doc: "",
    no: "____",
    date: currentDate.toJSON().split('T')[0],
    dueDate: nextMonthDate.toJSON().split('T')[0],
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
	}

  let qry = $state(org);
	let sharedUrl = $state('')

  let txt = $derived.by(() => {
    let trn = trns.en;
    if (trns[qry.lang]) {
      trn = { ...trn, ...trns[qry.lang] };
    }
    let txt = trns.en.docs[""];
    if (trn.docs[qry.doc]) {
      txt = { ...txt, ...trn.docs[""], ...trn.docs[qry.doc] };
    }
    return txt;
  })

  let amount = $derived.by(() => {
    let amount = 0;
    qry.qty.forEach((qty, index) => {
      amount += +qty * +qry.price[index];
    });
    return amount;
  })

  function formatNumber (value, option) {
		value = +value
    return value == 0 ? "" : value.toLocaleString(qry.lang, { minimumFractionDigits: 2, maximumFractionDigits: 2, ...option });
  }
  function formatDate (value, option) {
    return new Date(value).toLocaleDateString(qry.lang, { day: 'numeric', month: 'short', year: 'numeric' , ...option })
  }

	function uploadLogo (e) {
		const file = e.target.files[0];
		if (file) {
			const reader = new FileReader();
			reader.addEventListener('load', () => {
				qry.vendorLogo = reader.result;
			});
			reader.readAsDataURL(file);
		} else {
			qry.vendorLogo = '';
		}
	}
	function addRow () {
		qry.price.push(''); 
		qry.qty.push(''); 
		qry.desc.push(''); 
	}
	function deleteRow (index) {
		qry.price.splice(index, 1); 
		qry.qty.splice(index, 1); 
		qry.desc.splice(index, 1); 
	}
  function clearForm () {
    qry = { ...org, lang: qry.lang, doc: qry.doc };
		sharedUrl = ''
  }
  function saveLink () {
    const searchParams = new URLSearchParams();
    let vendorLogo = "";
    if (qry.vendorLogo.length > 100) {
      vendorLogo = qry.vendorLogo;
      qry.vendorLogo = ""
    }
    Object.entries(qry).forEach(([key, value]) => {
      if (Array.isArray(value)) {
        searchParams.append(key, value.join(","));
      } else {
        searchParams.append(key, value);
      }
    });
    qry.vendorLogo = vendorLogo;
		sharedUrl = "https://codepen.io/zummon/full/wvLrqBe?" + searchParams.toString()
    navigator.clipboard.writeText(sharedUrl);
  }
  onMount(() => {
    const searchParams = new URLSearchParams(location.search);

    Object.entries(org).forEach(([key, value]) => {
      if (searchParams.has(key)) {
        if (Array.isArray(value)) {
          qry[key] = searchParams.get(key).split(",");
        } else {
          qry[key] = searchParams.get(key);
        }
      }
    });
  })
</script>

<div class="flex flex-wrap justify-center px-2 py-4 lg:py-6 gap-4 print:hidden">
	{#each Object.keys(trns) as locale}
		{#each Object.keys(trns[locale].docs) as doc}
			<button class="font-medium text-lg cursor-pointer border-b-2 {qry.doc == doc ? 'text-neutral-500 border-transparent' : 'text-cyan-500 border-cyan-500'} {qry.lang == locale ? '' : 'hidden'}" onclick={() => {
				qry.lang = locale
				qry.doc = doc; 
				qry.whtRate = ['receipt'].includes(qry.doc) ? 0.01 : 0 
			}}>{trns[locale].docs[doc].title}</button>
		{/each}
    <button class="font-semibold text-lg cursor-pointer order-first border-b-2 {qry.lang == locale ? 'text-neutral-500 border-transparent' : 'text-green-500 border-green-500'}" onclick={() => {
			qry.lang = locale 
		}}>{trns[locale].title}</button>
  {/each}
  <button class="text-cyan-500 cursor-pointer" title="Clear forms" onclick={() => { clearForm() }}>
		<!-- https://fonts.google.com/icons?selected=Material+Symbols+Outlined:ink_eraser:FILL@0;wght@400;GRAD@0;opsz@40&icon.query=eraser&icon.size=32&icon.color=currentColor -->
		<svg class="size-8" xmlns="http://www.w3.org/2000/svg" viewBox="0 -960 960 960" fill="currentColor"><path d="M682-226.67h198.67V-160H615.33L682-226.67ZM182.67-160l-82.34-84.33q-19.66-19-19.5-46.67.17-27.67 18.5-47l452-484q18.34-19.33 45.88-19.33 27.55 0 46.46 19l203 209.66q19 19 19.66 47 .67 28-18.33 47L508.67-160h-326ZM482-226.67l320.67-342-204-210.66L148-292l64 65.33h270ZM480-480Z"/></svg>
	</button>
  <button class="text-cyan-500 cursor-pointer" title="Copy shareable link to clipboard" onclick={() => { saveLink() }}>
		<!-- https://fonts.google.com/icons?icon.query=link&icon.size=32&icon.color=currentColor&icon.style=Rounded&selected=Material+Symbols+Rounded:link:FILL@0;wght@400;GRAD@0;opsz@40 -->
		<svg class="size-8" xmlns="http://www.w3.org/2000/svg" viewBox="0 -960 960 960" fill="currentColor"><path d="M280-280q-83 0-141.5-58.5T80-480q0-83 58.5-141.5T280-680h133.33q14.17 0 23.75 9.62 9.59 9.61 9.59 23.83 0 14.22-9.59 23.72-9.58 9.5-23.75 9.5H280q-55.56 0-94.44 38.84-38.89 38.84-38.89 94.33 0 55.49 38.89 94.49 38.88 39 94.44 39h133.33q14.17 0 23.75 9.62 9.59 9.62 9.59 23.83 0 14.22-9.59 23.72-9.58 9.5-23.75 9.5H280Zm76.67-166.67q-14.17 0-23.75-9.61-9.59-9.62-9.59-23.84 0-14.21 9.59-23.71 9.58-9.5 23.75-9.5h246.66q14.17 0 23.75 9.61 9.59 9.62 9.59 23.84 0 14.21-9.59 23.71-9.58 9.5-23.75 9.5H356.67Zm190 166.67q-14.17 0-23.75-9.62-9.59-9.61-9.59-23.83 0-14.22 9.59-23.72 9.58-9.5 23.75-9.5H680q55.56 0 94.44-38.84 38.89-38.84 38.89-94.33 0-55.49-38.89-94.49-38.88-39-94.44-39H546.67q-14.17 0-23.75-9.62-9.59-9.62-9.59-23.83 0-14.22 9.59-23.72 9.58-9.5 23.75-9.5H680q83 0 141.5 58.5T880-480q0 83-58.5 141.5T680-280H546.67Z"/></svg>
	</button>
  <button class="text-green-500 cursor-pointer" title="Print" onclick={() => { print() }} >
		<!-- https://heroicons.com/solid printer -->
		<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="size-8">
			<path fill-rule="evenodd" d="M7.875 1.5C6.839 1.5 6 2.34 6 3.375v2.99c-.426.053-.851.11-1.274.174-1.454.218-2.476 1.483-2.476 2.917v6.294a3 3 0 0 0 3 3h.27l-.155 1.705A1.875 1.875 0 0 0 7.232 22.5h9.536a1.875 1.875 0 0 0 1.867-2.045l-.155-1.705h.27a3 3 0 0 0 3-3V9.456c0-1.434-1.022-2.7-2.476-2.917A48.716 48.716 0 0 0 18 6.366V3.375c0-1.036-.84-1.875-1.875-1.875h-8.25ZM16.5 6.205v-2.83A.375.375 0 0 0 16.125 3h-8.25a.375.375 0 0 0-.375.375v2.83a49.353 49.353 0 0 1 9 0Zm-.217 8.265c.178.018.317.16.333.337l.526 5.784a.375.375 0 0 1-.374.409H7.232a.375.375 0 0 1-.374-.409l.526-5.784a.373.373 0 0 1 .333-.337 41.741 41.741 0 0 1 8.566 0Zm.967-3.97a.75.75 0 0 1 .75-.75h.008a.75.75 0 0 1 .75.75v.008a.75.75 0 0 1-.75.75H18a.75.75 0 0 1-.75-.75V10.5ZM15 9.75a.75.75 0 0 0-.75.75v.008c0 .414.336.75.75.75h.008a.75.75 0 0 0 .75-.75V10.5a.75.75 0 0 0-.75-.75H15Z" clip-rule="evenodd" />
		</svg>	
	</button>
</div>

<div class="p-4 print:hidden text-center">
	<div class="truncate underline text-cyan-500">
		<a class="" target="_top" href={sharedUrl}>{sharedUrl}</a>
	</div>
	Select text you want to edit then type directly.<br>
	Click the logo to upload yours
</div>

<div class="max-w-3xl p-8 mx-auto bg-white shadow-md print:p-0 print:shadow-none" style='font-family: "Noto Serif Thai", serif;'>
  <div class="flex flex-wrap gap-2">
    <label class="cursor-pointer {qry.vendorLogo ? '' : 'print:hidden'}">
      <input class="hidden" type="file" onchange={(e) => { uploadLogo(e) }} />
      <img class="max-h-20" src={qry.vendorLogo} alt={txt.vendorLogo} />
    </label>
    <div class="">
      <div class="">
        <input class="bg-transparent border-0 p-0 field-sizing-content" type="text" placeholder={txt.vendor} bind:value={qry.vendor} />
      </div>
      <div class="">
        <input class="bg-transparent border-0 p-0 field-sizing-content" type="text" placeholder={txt.vendorid} bind:value={qry.vendorid} />
      </div>
      <div class="">
        <textarea class="bg-transparent border-0 p-0 field-sizing-content resize-none overflow-hidden" placeholder={txt.vendorAddress} bind:value={qry.vendorAddress}></textarea>
      </div>
    </div>
  </div>

  <div class="flex flex-wrap gap-2 my-2 items-center">
    <div class="grow">
      <div class="flex gap-2">
        <div class="">{txt.client}</div>
        <div class="">
					<input class="bg-transparent border-0 p-0 field-sizing-content" type="text" bind:value={qry.client} />
				</div>
      </div>
      <div class="">
        <input class="bg-transparent border-0 p-0 field-sizing-content" type="text" placeholder={txt.clientid} bind:value={qry.clientid} />
      </div>
      <div class="">
        <textarea class="bg-transparent border-0 p-0 field-sizing-content resize-none overflow-hidden" placeholder={txt.clientAddress} bind:value={qry.clientAddress}></textarea>
      </div>
    </div>
    <div class="">
      <h1 class="text-2xl">{txt.title}</h1>
      <div class="flex gap-2">
        <div class="">{txt.no}</div>
        <div class="">
					<input class="bg-transparent border-0 p-0 field-sizing-content" type="text" bind:value={qry.no} />
				</div>
      </div>
      <div class="flex gap-2">
        <div class="">{txt.date}</div>
        <div class="grow hidden print:block">{formatDate(qry.date)}</div>
        <input class="bg-transparent border-0 p-0 print:hidden" type="date" bind:value={qry.date} />
      </div>
      {#if !['receipt'].includes(qry.doc)}
        <div class="flex gap-2">
          <div class="">{txt.dueDate}</div>
          <div class="grow hidden print:block">{formatDate(qry.dueDate)}</div>
          <input class="bg-transparent border-0 p-0 print:hidden" type="date" bind:value={qry.dueDate} />
        </div>
      {/if}
      <div class="">{txt.payMethod}</div>
      <textarea class="w-full bg-transparent border-0 p-0 field-sizing-content resize-none overflow-hidden" bind:value={qry.payMethod}></textarea>
    </div>
  </div>

  <table class="w-full">
    <thead>
      <tr class="font-medium">
        <td class="">{txt.desc}</td>
        <td class="border-x border-neutral-400 text-center">{txt.qty}</td>
        <td class="border-x border-neutral-400 text-center">{txt.price}</td>
        <td class="text-right">{txt.amount}</td>
      </tr>
    </thead>
    <tbody>
      {#each qry.desc as _, index}
        <tr class="border-y border-neutral-400">
          <td class="break-all p-1" contenteditable bind:textContent={qry.desc[index]}></td>
          <td class="text-center p-1">
            <span class="hidden print:inline">{formatNumber(qry.qty[index])}</span>
            <input class="bg-transparent border-0 p-0 print:hidden w-20 text-center" type="number" bind:value={qry.qty[index]}>
          </td>
          <td class="text-center p-1">
            <span class="hidden print:inline">{formatNumber(qry.price[index])}</span>
            <input class="bg-transparent border-0 p-0 print:hidden w-20 text-center" type="number" bind:value={qry.price[index]}>
          </td>
          <td class="text-right p-1">
						&nbsp;<span class="">{formatNumber(+qry.qty[index] * +qry.price[index])}</span>
						<button class="text-red-500 cursor-pointer print:hidden" title="Delete this row" onclick={() => { deleteRow(index) }}>
							<!-- https://heroicons.com/micro x-mark -->
							<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 16 16" fill="currentColor" class="size-6">
								<path d="M5.28 4.22a.75.75 0 0 0-1.06 1.06L6.94 8l-2.72 2.72a.75.75 0 1 0 1.06 1.06L8 9.06l2.72 2.72a.75.75 0 1 0 1.06-1.06L9.06 8l2.72-2.72a.75.75 0 0 0-1.06-1.06L8 6.94 5.28 4.22Z" />
							</svg>
						</button>
					</td>
        </tr>
      {/each}
    </tbody>
    <tfoot>
      <tr class="">
        <td class="">
					<button class="text-green-500 cursor-pointer print:hidden" title="Add a new row" onclick={() => { addRow() }}>
						<!-- https://heroicons.com/micro plus -->
						<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 16 16" fill="currentColor" class="size-8">
							<path d="M8.75 3.75a.75.75 0 0 0-1.5 0v3.5h-3.5a.75.75 0 0 0 0 1.5h3.5v3.5a.75.75 0 0 0 1.5 0v-3.5h3.5a.75.75 0 0 0 0-1.5h-3.5v-3.5Z" />
						</svg>
					</button>
				</td>
        <td class="text-center border-r border-b border-neutral-400" colspan="2">{txt.subtotal}</td>
        <td class="text-right border-b border-neutral-400">{formatNumber(amount)}</td>
      </tr>
    </tfoot>
  </table>

  <div class="flex flex-wrap-reverse gap-4 my-2">
    <div class="grow">
      <div>{txt.note}</div>
      <textarea class="w-full bg-transparent border-0 p-0 field-sizing-content resize-none overflow-hidden" bind:value={qry.note}></textarea>
    </div>
    <div class="">
      <div class="flex gap-4">
        <div class="">
					<span class="">{txt.vat}</span> 
					<span class="hidden print:inline">{formatNumber(+qry.vatRate * 100)}%</span>
					<input class="bg-transparent border-0 p-0 print:hidden w-20 text-center" type="number" step="0.01" bind:value={qry.vatRate}>
				</div>
        <div class="grow text-right">{formatNumber(amount * +qry.vatRate)}</div>
      </div>
      {#if ['receipt'].includes(qry.doc)}  
        <div class="flex gap-4">
          <div class="">
						<span class="">{txt.wht}</span> 
						<span class="hidden print:inline">{formatNumber(+qry.whtRate * 100)}%</span>
						<input class="bg-transparent border-0 p-0 print:hidden w-20 text-center" type="number" step="0.01" bind:value={qry.whtRate}>
					</div>
          <div class="grow text-right">{formatNumber(amount * +qry.whtRate)}</div>
        </div>
      {/if}
      <div class="flex gap-4">
        <div class="">{txt.adjust}</div>
        <div class="grow text-right hidden print:block">{formatNumber(qry.adjust)}</div>
        <input class="grow bg-transparent border-0 p-0 print:hidden text-right" type="number" bind:value={qry.adjust} />
      </div>
      <div class="flex gap-4 font-medium">
        <div class="">{txt.total}</div>
        <div class="grow text-right">{formatNumber(amount + (amount * +qry.vatRate) + (amount * +qry.whtRate) + +qry.adjust)}</div>
      </div>
    </div>
  </div>

  <div class="flex flex-wrap justify-end gap-8">
    <div class="">
      <br /><br />
      <div class="border-t border-neutral-400" >{txt.vendorSign}</div>
    </div>
    <div class="">
      <br /><br />
      <div class="border-t border-neutral-400" >{txt.clientSign}</div>
    </div>
  </div>
</div>
