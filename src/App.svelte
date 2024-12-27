<script>
  import { onMount } from "svelte";

  let trns = {
    en: {
      title: "English",
      copy: "Copy",
      clear: "Clear",
      more: "Add",
      print: "Print",
      desc: "interactive invoice to edit and print",
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
          clientSign: "Receiver signature"
        }
      }
    },
    th: {
      title: "ไทย",
      copy: "สำเนา",
      clear: "ล้าง",
      more: "เพิ่ม",
      print: "ปริ้น",
      desc: "พิมพ์ ใบแจ้งหนี้ แก้ไข และปริ้น",
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
        }
      }
    }
  };

  let qry = $state({
    lang: "en",
    doc: "",
    no: "____",
    date: "________",
    dueDate: "________",
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

  let trn = $derived.by(() => {
    let trn = trns.en;
    if (trns[qry.lang]) {
      trn = { ...trn, ...trns[qry.lang] };
    }
    return trn;
  })
  let txt = $derived.by(() => {
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

  function done() {
    const searchParams = new URLSearchParams();
    let vendorLogo = "";
    if (qry.vendorLogo.length > 100) {
      vendorLogo = qry.vendorLogo;
    }
    Object.entries(qry).forEach(([key, value]) => {
      if (Array.isArray(value)) {
        searchParams.append(key, value.join(","));
      } else {
        searchParams.append(key, value);
      }
    });
    qry.vendorLogo = vendorLogo;
    navigator.clipboard.writeText(
      "https://codepen.io/zummon/full/wvLrqBe?" + searchParams.toString()
    );
  }
  function clear() {
    qry = { ...qry, lang: qry.lang, doc: qry.doc };
  }
  function formatNumber(number, option) {
    return number == 0
      ? ""
      : number.toLocaleString(qry.lang, {
          minimumFractionDigits: 2,
          maximumFractionDigits: 2,
          ...option
        });
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
  {#each Object.keys(trn.docs) as doc}
    <button class="font-medium text-lg border-b-2 border-cyan-500 {qry.doc == doc ? 'text-cyan-500 border-transparent' : 'hocus:text-cyan-500 hocus:border-transparent'}" onclick={() => { qry.doc = doc; qry.whtRate = ['receipt'].includes(qry.doc) ? 0.01 : 0 }} >{trn.docs[doc].title}</button>
  {/each}
  {#each Object.keys(trns) as locale}
    <button class="font-semibold text-lg border-b-2 border-green-500 {qry.lang == locale ? 'text-green-500 border-transparent' : 'hocus:text-green-500 hocus:border-transparent'}" onclick={() => { qry.lang = locale }} >{trns[locale].title}</button>
  {/each}
</div>

<div class="max-w-screen-lg mx-auto p-2 print:p-0">
  <div class="flex flex-wrap gap-2">
    <label class="{qry.vendorLogo ? '' : 'print:hidden'}">
      <input class="hidden" type="file" onchange={(e) => {
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
      }} />
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
        <div class="grow hidden print:block">{qry.date}</div>
        <input class="grow bg-transparent border-0 p-0 print:hidden" type="text" bind:value={qry.date} />
      </div>
      {#if !['receipt'].includes(qry.doc)}  
        <div class="flex gap-2">
          <div class="">{txt.dueDate}</div>
          <div class="grow hidden print:block">{qry.dueDate}</div>
          <input class="grow bg-transparent border-0 p-0 print:hidden" type="text" bind:value={qry.dueDate} />
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
          <td class="">
            <span class="hidden print:inline">{qry.desc[index]}</span>
            <input class="bg-transparent border-0 p-0 print:hidden w-80" bind:value={qry.desc[index]}>
          </td>
          <td class="text-center">
            <span class="hidden print:inline">{qry.qty[index]}</span>
            <input class="bg-transparent border-0 p-0 print:hidden w-20 text-center" type="number" bind:value={qry.qty[index]}>
          </td>
          <td class="text-center">
            <span class="hidden print:inline">{qry.price[index]}</span>
            <input class="bg-transparent border-0 p-0 print:hidden w-20 text-center" type="number" bind:value={qry.price[index]}>
          </td>
          <td class="text-right text-base">&nbsp;<span>{formatNumber(+qry.qty[index] * +qry.price[index])}</span></td>
        </tr>
      {/each}
    </tbody>
    <tfoot>
      <tr class="">
        <td class=""></td>
        <td class="text-center border-r border-neutral-400" colspan="2">{txt.subtotal}</td>
        <td class="text-right">{formatNumber(amount)}</td>
      </tr>
    </tfoot>
  </table>

  <div class="flex flex-wrap-reverse gap-2 my-2">
    <div class="grow">
      <div>{txt.note}</div>
      <div class="hidden print:block">{qry.note}</div>
      <textarea class="w-full bg-transparent border-0 p-0 print:hidden" bind:value={qry.note}></textarea>
    </div>
    <div class="">
      <div class="flex gap-2">
        <div class=""><span>{txt.vat}</span> <span class="hidden print:inline">{formatNumber(+qry.vatRate * 100)}</span><input class="bg-transparent border-0 p-0 print:hidden w-20 text-center" type="number" step="0.01" bind:value={qry.vatRate}>%</div>
        <div class="grow text-right">{formatNumber(amount * +qry.vatRate)}</div>
      </div>
      {#if ['receipt'].includes(qry.doc)}  
        <div class="flex gap-2">
          <div class=""><span>{txt.wht}</span> <span class="hidden print:inline">{formatNumber(+qry.whtRate * 100)}</span><input class="bg-transparent border-0 p-0 print:hidden w-20 text-center" type="number" step="0.01" bind:value={qry.whtRate}>%</div>
          <div class="grow text-right">{formatNumber(amount * +qry.whtRate)}</div>
        </div>
      {/if}
      <div class="flex gap-2">
        <div class="">{txt.adjust}</div>
        <div class="grow text-right hidden print:block">{qry.adjust}</div>
        <input class="grow bg-transparent border-0 p-0 print:hidden text-right" type="number" bind:value={qry.adjust} />
      </div>
      <div class="flex gap-2 font-medium">
        <div class="">{txt.total}</div>
        <div class="grow text-right">{formatNumber(amount + (amount * +qry.vatRate) + (amount * +qry.whtRate) + +qry.adjust)}</div>
      </div>
    </div>
  </div>

  <div class="flex flex-wrap justify-end gap-2">
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

<div class="flex flex-wrap justify-center px-2 py-4 lg:py-6 gap-4 print:hidden">
  <button class="font-semibold text-lg border-b-2 border-neutral-500 hocus:text-green-500 hocus:border-transparent" onclick={() => { qry.price.push(''); qry.qty.push(''); qry.desc.push(''); }} >{trn.more}</button>
  <button class="font-semibold text-lg border-b-2 border-neutral-500 hocus:text-neutral-500 hocus:border-transparent" onclick={() => { clear() }} >{trn.clear}</button>
  <button class="font-semibold text-lg border-b-2 border-cyan-500 hocus:text-cyan-500 hocus:border-transparent" onclick={() => { done(); }} >{trn.copy}</button>
  <button class="font-semibold text-lg border-b-2 border-green-500 hocus:text-green-500 hocus:border-transparent" onclick={() => { print() }} >{trn.print}</button>
</div>

