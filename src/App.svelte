<script>
  import { onMount } from "svelte";
	let trns = {
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

  let qry = $state({
    lang: "en",
    doc: "",
    no: "____",
    date: currentDate.toJSON().split('T')[0],
    dueDate: nextMonthDate.toJSON().split('T')[0],
    payMethod: "________",
    vendor: "____",
    vendorid: "____",
    vendorAddress: "________",
    vendorLogo: "",
    client: "____",
    clientid: "____",
    clientAddress: "________",
    desc: ["", "", "", ""],
    price: ["", "", "", ""],
    qty: ["", "", "", ""],
    vatRate: "0.07",
    whtRate: "0.00",
    adjust: "",
    note: "________"
  });

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
    return (+value) == 0 ? "" : (+value).toLocaleString(qry.lang, { minimumFractionDigits: 2, maximumFractionDigits: 2, ...option });
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
    qry = { ...qry, lang: qry.lang, doc: qry.doc };
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
    navigator.clipboard.writeText("https://codepen.io/zummon/full/wvLrqBe?" + searchParams.toString());
  }
  onMount(() => {
    const searchParams = new URLSearchParams(location.search);

    Object.entries(qry).forEach(([key, value]) => {
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
  <button class="text-neutral-500 cursor-pointer" title="Clear forms" onclick={() => { clearForm() }}>
		<!-- https://heroicons.com/solid trash -->
		<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="size-8">
			<path fill-rule="evenodd" d="M16.5 4.478v.227a48.816 48.816 0 0 1 3.878.512.75.75 0 1 1-.256 1.478l-.209-.035-1.005 13.07a3 3 0 0 1-2.991 2.77H8.084a3 3 0 0 1-2.991-2.77L4.087 6.66l-.209.035a.75.75 0 0 1-.256-1.478A48.567 48.567 0 0 1 7.5 4.705v-.227c0-1.564 1.213-2.9 2.816-2.951a52.662 52.662 0 0 1 3.369 0c1.603.051 2.815 1.387 2.815 2.951Zm-6.136-1.452a51.196 51.196 0 0 1 3.273 0C14.39 3.05 15 3.684 15 4.478v.113a49.488 49.488 0 0 0-6 0v-.113c0-.794.609-1.428 1.364-1.452Zm-.355 5.945a.75.75 0 1 0-1.5.058l.347 9a.75.75 0 1 0 1.499-.058l-.346-9Zm5.48.058a.75.75 0 1 0-1.498-.058l-.347 9a.75.75 0 0 0 1.5.058l.345-9Z" clip-rule="evenodd" />
		</svg>
	</button>
  <button class="text-cyan-500 cursor-pointer" title="Copy shareable link to clipboard" onclick={() => { saveLink() }}>
		<!-- https://heroicons.com/solid document-duplicate -->
		<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="size-8">
			<path d="M7.5 3.375c0-1.036.84-1.875 1.875-1.875h.375a3.75 3.75 0 0 1 3.75 3.75v1.875C13.5 8.161 14.34 9 15.375 9h1.875A3.75 3.75 0 0 1 21 12.75v3.375C21 17.16 20.16 18 19.125 18h-9.75A1.875 1.875 0 0 1 7.5 16.125V3.375Z" />
			<path d="M15 5.25a5.23 5.23 0 0 0-1.279-3.434 9.768 9.768 0 0 1 6.963 6.963A5.23 5.23 0 0 0 17.25 7.5h-1.875A.375.375 0 0 1 15 7.125V5.25ZM4.875 6H6v10.125A3.375 3.375 0 0 0 9.375 19.5H16.5v1.125c0 1.035-.84 1.875-1.875 1.875h-9.75A1.875 1.875 0 0 1 3 20.625V7.875C3 6.839 3.84 6 4.875 6Z" />
		</svg>		
	</button>
  <button class="text-green-500 cursor-pointer" title="Print" onclick={() => { print() }} >
		<!-- https://heroicons.com/solid printer -->
		<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="size-8">
			<path fill-rule="evenodd" d="M7.875 1.5C6.839 1.5 6 2.34 6 3.375v2.99c-.426.053-.851.11-1.274.174-1.454.218-2.476 1.483-2.476 2.917v6.294a3 3 0 0 0 3 3h.27l-.155 1.705A1.875 1.875 0 0 0 7.232 22.5h9.536a1.875 1.875 0 0 0 1.867-2.045l-.155-1.705h.27a3 3 0 0 0 3-3V9.456c0-1.434-1.022-2.7-2.476-2.917A48.716 48.716 0 0 0 18 6.366V3.375c0-1.036-.84-1.875-1.875-1.875h-8.25ZM16.5 6.205v-2.83A.375.375 0 0 0 16.125 3h-8.25a.375.375 0 0 0-.375.375v2.83a49.353 49.353 0 0 1 9 0Zm-.217 8.265c.178.018.317.16.333.337l.526 5.784a.375.375 0 0 1-.374.409H7.232a.375.375 0 0 1-.374-.409l.526-5.784a.373.373 0 0 1 .333-.337 41.741 41.741 0 0 1 8.566 0Zm.967-3.97a.75.75 0 0 1 .75-.75h.008a.75.75 0 0 1 .75.75v.008a.75.75 0 0 1-.75.75H18a.75.75 0 0 1-.75-.75V10.5ZM15 9.75a.75.75 0 0 0-.75.75v.008c0 .414.336.75.75.75h.008a.75.75 0 0 0 .75-.75V10.5a.75.75 0 0 0-.75-.75H15Z" clip-rule="evenodd" />
		</svg>	
	</button>
</div>

<div class="p-4 print:hidden text-center">
	Select text you want to edit then type directly.<br>
	Click the logo to upload yours
</div>

<div class="max-w-3xl p-8 mx-auto bg-white shadow-md print:p-0 print:shadow-none" style='font-family: "Noto Serif Thai", serif;'>
  <div class="flex flex-wrap gap-2">
    <label class="cursor-pointer {qry.vendorLogo ? '' : 'print:hidden'}">
      <input class="hidden" type="file" onchange={(e) => {uploadLogo(e)}} />
      <img class="max-h-20" src={qry.vendorLogo} alt={txt.vendorLogo} />
    </label>
    <div class="">
      <div class="flex gap-2">
        <div class="">{txt.vendor}</div>
        <div class="grow hidden print:block">{qry.vendor}</div>
        <input class="grow bg-transparent border-0 p-0 print:hidden" type="text" bind:value={qry.vendor} />
      </div>
      <div class="flex gap-2">
        <div class="">{txt.vendorid}</div>
        <div class="grow hidden print:block">{qry.vendorid}</div>
        <input class="grow bg-transparent border-0 p-0 print:hidden" type="text" bind:value={qry.vendorid} />
      </div>
      <div class="flex gap-2">
        <div class="">{txt.vendorAddress}</div>
        <div class="grow hidden print:block">{qry.vendorAddress}</div>
        <textarea class="grow bg-transparent border-0 p-0 print:hidden" bind:value={qry.vendorAddress}></textarea>
      </div>
    </div>
  </div>

  <div class="flex flex-wrap gap-2 my-2 items-center">
    <div class="grow">
      <div class="flex gap-2">
        <div class="">{txt.client}</div>
        <div class="grow hidden print:block">{qry.client}</div>
        <input class="grow bg-transparent border-0 p-0 print:hidden" type="text" bind:value={qry.client} />
      </div>
      <div class="flex gap-2">
        <div class="">{txt.clientid}</div>
        <div class="grow hidden print:block">{qry.clientid}</div>
        <input class="grow bg-transparent border-0 p-0 print:hidden" type="text" bind:value={qry.clientid} />
      </div>
      <div class="flex gap-2">
        <div class="">{txt.clientAddress}</div>
        <div class="grow hidden print:block">{qry.clientAddress}</div>
        <textarea class="grow bg-transparent border-0 p-0 print:hidden" bind:value={qry.clientAddress}></textarea>
      </div>
    </div>
    <div class="">
      <h1 class="text-2xl">{txt.title}</h1>
      <div class="flex gap-2">
        <div class="">{txt.no}</div>
        <div class="grow hidden print:block">{qry.no}</div>
        <input class="grow bg-transparent border-0 p-0 print:hidden" type="text" bind:value={qry.no} />
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
      <div class="hidden print:block">{qry.payMethod}</div>
      <textarea class="w-full bg-transparent border-0 p-0 print:hidden" bind:value={qry.payMethod}></textarea>
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
            <span class="hidden print:inline">{qry.qty[index]}</span>
            <input class="bg-transparent border-0 p-0 print:hidden w-20 text-center" type="number" bind:value={qry.qty[index]}>
          </td>
          <td class="text-center p-1">
            <span class="hidden print:inline">{qry.price[index]}</span>
            <input class="bg-transparent border-0 p-0 print:hidden w-20 text-center" type="number" bind:value={qry.price[index]}>
          </td>
          <td class="text-right p-1">
						&nbsp;<span class="">{formatNumber(+qry.qty[index] * +qry.price[index])}</span>
						<button class="text-red-500 cursor-pointer print:hidden" title="Delete this row" onclick={() => { deleteRow(index) }}>
							<!-- https://heroicons.com/micro minus -->
							<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 16 16" fill="currentColor" class="size-6">
								<path d="M3.75 7.25a.75.75 0 0 0 0 1.5h8.5a.75.75 0 0 0 0-1.5h-8.5Z" />
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
        <td class="text-center border-r border-neutral-400" colspan="2">{txt.subtotal}</td>
        <td class="text-right">{formatNumber(amount)}</td>
      </tr>
    </tfoot>
  </table>

  <div class="flex flex-wrap-reverse gap-4 my-2">
    <div class="grow">
      <div>{txt.note}</div>
      <div class="hidden print:block">{qry.note}</div>
      <textarea class="w-full bg-transparent border-0 p-0 print:hidden" bind:value={qry.note}></textarea>
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
        <div class="grow text-right hidden print:block">{qry.adjust}</div>
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
