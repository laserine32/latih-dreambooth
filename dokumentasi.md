# Dreambooth

## Pengantar
[DreamBooth](https://arxiv.org/abs/2208.12242) adalah metode untuk mempersonalisasi model *text-to-image* seperti *Stable Diffusion* dengan hanya memberikan beberapa (3-5) gambar subjek. Hal ini memungkinkan model untuk menghasilkan gambar kontekstual dari subjek dalam adegan, pose, dan tampilan yang berbeda.
Tapi, Cara kerja DreamBooth adalah dengan langsung *pre-trained models* dan memperbarui bobotnya. Ini adalah metode yang lebih powerfull, namun hasil dari proses ini adalah model yang benar-benar baru dengan ukuran yang kira-kira sama dengan *pre-trained models*. Contohnya jika memaki *pre-trained models* *Stable Diffusion 1.5* maka hasilnya juga akan berukuran sekitar 4m5 Gb

## Pro kontra
perbandingan antara Textual Inversion, DreamBooth dan LoRA

Tipe | Jumlah Dataset | Ukuran Output | Keterangan
-- | -- | -- | --
Textual Inversion | Minimal 3-5, idealnya 20-30 | < 100 KB embeddings file | **OK Quality**, berfungsi lebih baik untuk mencampurkan kembali konsep yang ada dalam model dasar. Kamu bisa menggabungkan beberapa *embedding* saat runtime untuk menghasilkan beberapa konsep, satu *embedding* biasanya mewakili satu konsep atau gaya.
DreamBooth | Minimal 3-5, idealnya 20-30 | ~4.5 GB full model | **Great Quality**, bekerja dengan baik untuk karakter dan style. Karena ini menghasilkan model lengkap baru, kamu harus melatih banyak karakter dalam satu pelatihan. Mencampur style bisa jadi sulit untuk dilakukan.
LoRA | 20-50 untuk karakter, 50-200 untuk style | ~50-200 MB tensor files | **Good Quality**, diantara *Textual Inversion* dan *embedding*. Kamu bisa menerapkan beberapa LoRA saat runtime dan sangat fleksibel untuk dipadupadankan.

# Training
[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/laserine32/poor-wfsn/blob/main/OneClickFaceSwapper.ipynb)

## Dependensi
![Dependensi](https://user-images.githubusercontent.com/98224593/208588687-01773814-9ac5-4371-93e8-509f38f975be.jpg)

## Download Model
Pilih model yang spesifik, maksudnya jika kamu mau menghasilkan gambar anime maka cari model anime, kalau mau realistis ya pakai model yang realistis.
Ini step yg penting, karena model inilah yang akan mencerminkan hasil akhir yg kita harapkan nanti. Jadi jangan sembarangan pilih.
Kamu bisa cari model di [🤗 Huggingface](https://huggingface.co/), [civit.ai](https://civitai.com/) atau penyedia model lainnya. 
Balik lagi ke google colab, di section model download, pilih 1.5 di Model_Version. Lalu di MODEL_LINK, paste link model dari google drive atau copy link download. Lalu run cell tersebut, maka Google Colab akan secara cepat ngedownload model kamu.


## Create Session
Selanjutnya, masuk ke sesi Create/Load Session, disini kamu bebas mau namain apa, ini kayak judul sesi aja sih. Misal kamu mau namain shrk kayak dibawah ini juga gpp, itu maksudnya Shrek disingkat jadi shrk (karena yg ngebuat gambar tutorial ini lagi pengen ngetraining wajah Shrek).

![create session](https://user-images.githubusercontent.com/98224593/208593518-538167d1-6072-4acd-9b98-cc697a83d59a.jpg)

Nah,, gunanya Create/Load Session adalah, jika kamu ditengah jalan lagi bosen terus pengen di pause dulu, atau ketika sudah selesai dapetin hasil tapi pengen ditingkatin/improve lagi. Bisa nih nge load sessionnya dengan cara ketik nama session yang kamu maksud. Karena session yang kamu ketik disini akan otomatis ke save di GDrive, kalau misalkan di GDrive kamu ada folder shrk beserta isinya, dan kamu tulis shrk disitu, maka otomatis google colab akan ngeload semua progress kamu.

## Persiapan Dataset
Back to topic, lanjut lagi ke **Persiapan Dataset**, disini step yg paling super krusial. Memang untuk ngebuat art AI sebenernya mudah. Tapi yg bikin mager adalah ngumpulin dataset target kita. Fotonya harus foto wajah dengan kriteria sebagai berikut:

- Foto wajah yg high quality
- Foto wajah dengan berbagai ekspresi
- Foto wajah dengan berbagai kondisi pencahayaan/lighting
- Foto wajah dari berbagai angle
- Satu target paling sedikit 10 foto
- Foto harus di crop 1:1 (kotak) dengan dimensi 512x512
- 1 foto hanya ada 1 wajah

Tambahan kalau mau ai bisa menghasilkan gambar yang punya postur mirip dengan target, bisa pakai komposisi dataset sebagai berikut:

- 60% close up 
- 25% half body
- 15% full body

Sebenernya gak harus ngikutin syarat2 diatas juga bisa2 aja (pake 1 foto juga bisa), tapi dijamin pasti hasilnya kurang memuaskan. Semakin banyak dan semakin bervariasi foto target kita, maka AI-nya akan semakin banyak belajar sehingga bisa membuat art-art yang berkualitas dan akurat.

Contoh dataset adalah seperti digambar dibawah ini.

![contoh dataset](https://i.imgur.com/d2lD3rz.jpeg)

Bisa dilihat, dari kumpulan foto bapak Green Goblin diatas, ekspresinya macem2. Terus diambil dari berbagai angle (dari depan, samping dikit). Terus lightingnya juga beda2, ada yg ditempat terang, ada yg ditempat gelap. Nah, kumpulan foto kyk gini yg bisa menghasilkan model AI yang bagus. Nanti tinggal di run promptnya, dapet macem2 foto dengan muka bapaknya Harry ini.

Kalau sudah di kumpulkan, kamu bisa resize fotonya pakai [BRIME](https://www.birme.net/) Setelah di resize, jangan lupa direname namanya DENGAN nama yang konsonannya sedikit!

Harus susah dibaca! tujuannya adalah biar si AI ini bisa ngebedain target kamu dengan kata2 umum. Misal kamu mau edit wajah Budi Doremi, kalau misalnya file fotonya kamu namain budidoremi.jpeg nanti si AI akan ngebaca kata "doremi", akibatnya nanti pas kamu ketikan "photo of budidoremi" si AI akan kebingunan buat nyiptain foto budidoremi dan "doremi" yang merupakan kata yg umum. Akibatnya adalah, hasil akhirnya kurang memuaskan.

![sherk dataset](https://user-images.githubusercontent.com/98224593/208595856-2e806936-92e8-4781-ad4b-52aac1fe2ec8.jpg)

Misalkan shrk seperti ini, kamu rename semuanya dengan nama yg sama, dan biarkan dibelakangnya diikutin angka dalam kurung. Setelah semua sudah oke, tinggal klik upload seperti gambar dibawah

![upload image](https://user-images.githubusercontent.com/98224593/208600246-d41a0247-8ee8-40ba-bbbb-11ec2eada612.jpg)

## Training Model
Kalau sudah, kita **skip Caption dan Concept Images**. Kita langsung menuju training dan start dreambooth. Aturannya adalah UNET Training Steps harus mengikuti rumus berikut

```
(jumlah foto)*100 = jumlah UNet_Training_Steps
```

Jadi misalkan kamu ada 5 foto shrek, maka jumlah UNET training stepnya adalah 500. Kalau kamu ada 40 foto target kamu, maka UNet_Training_Steps adalah 4000. Ingat!! jangan kebanyakan dan jangan kedikitan. Kebanyakan bukanlah selalu berarti bagus, karena nanti model kamu akan overfitting (silahkan cari sendiri di google) dan kalau kedikitan akan underiftting. Jadi disarankan pakai rumus diatas.

![trainig](https://user-images.githubusercontent.com/98224593/208623229-bd67891c-b830-4eff-8eab-6b42f0223083.jpg)

Setelah itu tinggal run Cellnya. Inilah proses yang paling lama. 1500 steps itu sekitar 30 menitan, jadi kalau fotonya banyak, bisa berjam2 trainingnya. Nah, ini cukup kamu tinggal aja, gk usah kamu tonton. **YANG PENTING**, tabs google colabnya jangan ditutup! Kalau google colab kamu disconnect dan kamu gak simpen `Save_Checkpoint_Every_n_Steps`, maka kamu harus ngulang dari awal lagi. Maka dari itu kamu bisa pilih `Save_Checkpoint_Every_n_Steps` dan saran isi aja every 2000-3000 steps, karena backup yangg disave otomatis ini akan makan storage google drive kamu. Jadi USAHAKAN google drive kamu kosong banyak ya!!!

KALAU model sudah jadi, model akan otomatis ke save di google drive dengan `nama session.ckpt`.
