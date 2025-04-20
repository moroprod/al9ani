<script>
    import QrCode from "svelte-qrcode";
    import { PDFDocument } from "pdf-lib";
    import QRCode from "qrcode";

    let menuOpened = false;
    let blur = true;
    let language = "ar";
    let NameInput = "";
    let PhoneInput = "";
    let AdressInput = "";
    let EmailInput = "";
    let qrValue = null;
    let contact = false;

    function generateQr(text,text2,text3,text4) {
        qrValue = "If you find this item please contact : Name: " + text + " Phone: " + text2 + " " + text3 + " " + text4;
        if (text && text2) {
            blur = false;
        }else{
            blur = true;
        }
    }

    $: if (NameInput || PhoneInput) {
        generateQr(NameInput,PhoneInput,AdressInput,EmailInput);
    }
    else{
        qrValue = null;
    }

    const content = {
        ar: {
            mainHeading: "!استحفظ على ممتلكاتك",
            subHeading1: "ديما تنسى و إلا تضيع دبشك ؟",
            subHeading2: "تحب طريقة تنجم تضمن بيها استرجاع ممتلكاتك ؟",
            stage2Text: "👇 المرحلة 2 : قم بتحميل الملف من هنا",
            downloadButton: "PDF تحميل ملف",
            stage3Text: "المرحلة 3 : قم بطباعة الملف و قصّ القصاصات ✂️",
            stage1Text: "المرحلة 1 : قم بكتابة معلوماتك",
            nameLabel: "الإسم و اللقب 🙍",
            namePlaceholder: "مثال: طه بن علي",
            phoneLabel: "رقم الهاتف 📱",
            phonePlaceholder: "مثال: +(216) 00000000",
            addressLabel: "(العنوان (اختياري 🗺️",
            addressPlaceholder: "مثال: تونس، تونس",
            emailLabel: "(البريد الإلكتروني (اختياري 📧",
            emailPlaceholder: "مثال: email@example.com",
            aboutHeading: "شنية ألقاني؟",
            aboutText: "برشا ناس ديما تضيع ممتلكاتها، ما فما حتى حل الا انك تلوج في صفحات الفيسبوك عليها ... علاش ما تستعملش طريقة ساهلة و مجانية باش تضمن رجوع ممتلكاتك اذا لقاها شخص ضائعة",
            contactButton: "اتصل بنا",
        },
        en: {
            mainHeading: "Protect Your Belongings!",
            subHeading1: "Do you often lose or forget your items?",
            subHeading2: "Would you like a way to guarantee their return if found?",
            stage2Text: "👇 Step 2: Download the file from here",
            downloadButton: "Download PDF File",
            stage3Text: "Step 3: Print the file and cut out the slips ✂️",
            stage1Text: "Step 1: Enter Your Information",
            nameLabel: "Full Name 🙍",
            namePlaceholder: "Example: John Doe",
            phoneLabel: "Phone Number 📱",
            phonePlaceholder: "Example: +(216) 00000000",
            addressLabel: "(Optional) Address 🗺️",
            addressPlaceholder: "Example: Tunis, Tunisia",
            emailLabel: "(Optional) Email 📧",
            emailPlaceholder: "Example: email@example.com",
            aboutHeading: "What is Al9ani?",
            aboutText: "Many people often lose their belongings, and the only option is searching on social media... Why not use an easy and free way to guarantee that your belongings are returned if someone finds them?",
            contactButton: "Contact Us",
        }
    };
    async function downloadPdf() {
        const qrCodeDataUrl = await QRCode.toDataURL(qrValue);

        
        const pdfDoc = await PDFDocument.create();
        const page = pdfDoc.addPage([600, 800]);
        const qrImage = await pdfDoc.embedPng(qrCodeDataUrl);

        const qrSize = 100;
        const rows = 6;
        const columns = 5;

        
        for (let row = 0; row < rows; row++) {
            for (let col = 0; col < columns; col++) {
                page.drawImage(qrImage, {
                    x: col * (qrSize + 20),
                    y: 800 - (row + 1) * (qrSize + 20),
                    width: qrSize,
                    height: qrSize,
                });
            }
        }

       
        const pdfBytes = await pdfDoc.save();
        const blob = new Blob([pdfBytes], { type: "application/pdf" });
        const link = document.createElement("a");
        link.href = URL.createObjectURL(blob);
        link.download = "qr_code.pdf";
        link.click();
    }
    function toggleLanguage(lang) {
        language = lang;
    }
</script>

<main>
    <header class="w-full h-12 flex items-center md:justify-around justify-between md:px-0 px-10 border-b">
        <a href="/"><img src="logo.png" alt="logo" width="90"></a>
        <div class="md:flex font-light hidden">
            <button class="flex h-12 items-center gap-2 hover:bg-gray-100 p-4 transition" on:click={() => toggleLanguage('ar')}>
                <img src="https://cdnjs.cloudflare.com/ajax/libs/flag-icon-css/3.2.1/flags/4x3/tn.svg" alt="tn" width="20"> عربي
            </button>
            <button class="flex h-12 items-center gap-2 hover:bg-gray-100 p-4 transition" on:click={() => toggleLanguage('en')}>
                <img src="https://cdnjs.cloudflare.com/ajax/libs/flag-icon-css/3.2.1/flags/4x3/gb.svg" alt="en" width="20"> English
            </button>
            <button on:click={()=>{contact = true}} class="flex h-12 items-center gap-2 hover:bg-gray-100 p-4 transition">
                <svg xmlns="http://www.w3.org/2000/svg" width="1.3em" height="1.3em" viewBox="0 0 24 24"><path fill="black" d="M4.616 19q-.691 0-1.153-.462T3 17.384V6.616q0-.691.463-1.153T4.615 5h14.77q.69 0 1.152.463T21 6.616v10.769q0 .69-.463 1.153T19.385 19zM12 12.116L4 6.885v10.5q0 .269.173.442t.443.173h14.769q.269 0 .442-.173t.173-.443v-10.5zM12 11l7.692-5H4.308zM4 6.885V6v11.385q0 .269.173.442t.443.173H4z"/></svg>
                {content[language].contactButton}
            </button>
        </div>
        <div class="md:hidden flex">
            <button on:click={() => { menuOpened = !menuOpened; }}>
                {#if !menuOpened}
                <svg xmlns="http://www.w3.org/2000/svg" width="1.5em" height="1.5em" viewBox="0 0 48 48"><path fill="none" stroke="red" stroke-linecap="round" stroke-linejoin="round" stroke-width="4" d="M7.95 11.95h32m-32 12h32m-32 12h32"/></svg>
                {:else}
                <svg xmlns="http://www.w3.org/2000/svg" width="1.5em" height="1.5em" viewBox="0 0 24 24"><path fill="red" d="M6.4 19L5 17.6l5.6-5.6L5 6.4L6.4 5l5.6 5.6L17.6 5L19 6.4L13.4 12l5.6 5.6l-1.4 1.4l-5.6-5.6z"/></svg>
                {/if}
            </button>
        </div>
    </header>

    {#if menuOpened}
        <div class="w-full h-auto bg-white border-b border-red-600 absolute">
            <button class="flex h-12 items-center gap-2 hover:bg-gray-100 p-4 transition" on:click={() => toggleLanguage('ar')}>
                <img src="https://cdnjs.cloudflare.com/ajax/libs/flag-icon-css/3.2.1/flags/4x3/tn.svg" alt="tn" width="20"> عربي
            </button>
            <button class="flex h-12 items-center gap-2 hover:bg-gray-100 p-4 transition" on:click={() => toggleLanguage('en')}>
                <img src="https://cdnjs.cloudflare.com/ajax/libs/flag-icon-css/3.2.1/flags/4x3/gb.svg" alt="en" width="20"> English
            </button>
            <button on:click={()=>{contact = true}} class="flex h-12 items-center gap-2 hover:bg-gray-100 p-4 transition">
                <svg xmlns="http://www.w3.org/2000/svg" width="1.3em" height="1.3em" viewBox="0 0 24 24"><path fill="black" d="M4.616 19q-.691 0-1.153-.462T3 17.384V6.616q0-.691.463-1.153T4.615 5h14.77q.69 0 1.152.463T21 6.616v10.769q0 .69-.463 1.153T19.385 19zM12 12.116L4 6.885v10.5q0 .269.173.442t.443.173h14.769q.269 0 .442-.173t.173-.443v-10.5zM12 11l7.692-5H4.308zM4 6.885V6v11.385q0 .269.173.442t.443.173H4z"/></svg>
                {content[language].contactButton}
            </button>
        </div>
    {/if}

    <section class="w-full md:h-56 h-96 gradient flex items-center justify-around md:px-20 px-10 flex-col md:flex-row">
        <img src="header-pic.png" alt="header">
        <div class="text-right text-white font-light">
            <h2 class="text-5xl font-medium">{content[language].mainHeading}</h2>
            <p class="text-md">{content[language].subHeading1}</p>
            <p class="text-md">{content[language].subHeading2}</p>
        </div>
    </section>

    <section class="flex w-full h-full justify-around md:flex-row flex-col px-10 lg:px-64 md:px-30 py-6 gap-12">
        {#if qrValue}
        <div class="w-full">
            <div class="flex w-full flex-col items-center text-center h-full gap-2">
                <QrCode value="{qrValue}" size="300"/>
            </div>
        </div>
        {:else}
        <div class="w-full">
            <div class="flex w-full flex-col items-center text-center h-full gap-2">
                <h2 class="text-xl font-bold text-red-800">{content[language].aboutHeading}</h2>
                <p class="border-b pb-4">{content[language].aboutText}</p>
                <a href="https://moroprod.xyz" class="text-sm text-black/70">MoroProd &copy;2024</a>
            </div>
        </div>
        {/if}

        


        {#if blur}
        <div class="w-full flex flex-col text-center gap-2 blur-sm">
            <p class="text-xl text-[#4a4a4a] font-light">{content[language].stage2Text}</p>
            {#if blur}
            <button class="py-2 rounded-md w-full bg-red-600 text-white text-xl" disabled>{content[language].downloadButton}</button>
            {:else}
            <button class="py-2 rounded-md w-full bg-red-600 text-white text-xl">{content[language].downloadButton}</button>
            {/if}
            <p class="text-xl text-[#4a4a4a] font-light">{content[language].stage3Text}</p>
            <img src="mini-tuto.png" alt="tutorial">
        </div>
        {:else}
        <div class="w-full flex flex-col text-center gap-2">
            <p class="text-xl text-[#4a4a4a] font-light">{content[language].stage2Text}</p>
            <button on:click={downloadPdf}  class="py-2 rounded-md w-full bg-red-600 text-white text-xl">{content[language].downloadButton}</button>
            <p class="text-xl text-[#4a4a4a] font-light">{content[language].stage3Text}</p>
            <img src="mini-tuto.png" alt="tutorial">
        </div>
        {/if}

        <div class="w-full flex flex-col text-right gap-2">
            <p class="text-xl text-right text-[#4a4a4a] font-light">{content[language].stage1Text}</p>
            <label for="name" class="font-bold">{content[language].nameLabel}</label>
            <input id="name" bind:value={NameInput} class="w-full py-1 px-1 text-right border rounded-md focus:border-red-500 focus:outline-none" type="text" placeholder={content[language].namePlaceholder}>
            <label for="phone" class="font-bold">{content[language].phoneLabel}</label>
            <input id="phone" bind:value={PhoneInput} class="w-full py-1 px-1 text-right border rounded-md focus:border-red-500 focus:outline-none" type="number" placeholder={content[language].phonePlaceholder}>
            <label for="address" class="font-bold">{content[language].addressLabel}</label>
            <input id="address" bind:value={AdressInput} class="w-full py-1 px-1 text-right border rounded-md focus:border-red-500 focus:outline-none" type="text" placeholder={content[language].addressPlaceholder}>
            <label for="email" class="font-bold">{content[language].emailLabel}</label>
            <input id="email" bind:value={EmailInput} class="w-full py-1 px-1 text-right border rounded-md focus:border-red-500 focus:outline-none" type="text" placeholder={content[language].emailPlaceholder}>
        </div>
    </section>
    {#if contact}
    <section class="h-screen w-screen absolute z-50 flex items-center justify-center top-0 bg-black/80 backdrop-blur">
        <div class="lg:w-2/5 w-4/5 h-80 bg-white rounded-md border border-red-300 flex flex-col items-center justify-center">
            <!-- svelte-ignore a11y_consider_explicit_label -->
            <button on:click={()=>{contact = false}} class="absolute top-5 right-5">
                <svg xmlns="http://www.w3.org/2000/svg" width="1.5em" height="1.5em" viewBox="0 0 24 24"><path fill="white" d="M6.4 19L5 17.6l5.6-5.6L5 6.4L6.4 5l5.6 5.6L17.6 5L19 6.4L13.4 12l5.6 5.6l-1.4 1.4l-5.6-5.6z"/></svg>
            </button>
            <form class="flex flex-col w-full p-10" action="https://airform.io/abdolamr26@gmail.com" method="post">
                <h2 class="lg:text-3xl text-xl font-bold text-center mb-2 text-red-600">Fill out the form with your information:</h2>
                <label for="email">Email:</label>
                <input id="email" class="w-full py-1 px-1 text-left border rounded-md focus:border-red-500 focus:outline-none" type="email" required name="email">
                <label for="description">Description:</label>
                <textarea id="description" class="border rounded-md focus:border-red-500 focus:outline-none p-1" name="description" required></textarea>
                <button type="submit" class="py-2 mt-4 rounded-md w-full bg-red-600 text-white text-xl">Submit</button>
            </form>
        </div>

    </section>
    {/if}
</main>

<style>
    .gradient {
        background: linear-gradient(108.4deg, rgb(253, 44, 56) 3.3%, rgb(176, 2, 12) 98.4%);
    }
</style>
