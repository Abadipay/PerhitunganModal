
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Perhitungan OrderKuota</title>
    <style>
       body {
    font-family: 'Poppins', sans-serif;
    background-color: #f0f0f0;
    margin: 0;
    padding: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}

form {
    width: 100%;
    max-width: 400px;
    padding: 20px;
    background-color: white;
    border-radius: 12px;
    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
    margin: 20px;
    box-sizing: border-box; /* Ensure padding is included in width */
}

h1 {
    text-align: center;
    margin-bottom: 20px;
    font-size: 24px;
    color: #333;
}

label {
    font-size: 14px;
    color: #555;
    margin-bottom: 6px;
    display: block;
}

.input-container {
    position: relative;
    margin-bottom: 15px;
}

.input-container input {
    width: 100%;
    padding: 12px 12px 12px 60px; /* Adjust padding for prefix */
    border: 1px solid #ccc;
    border-radius: 8px;
    font-size: 16px;
    box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.05);
    transition: border-color 0.3s, box-shadow 0.3s;
    box-sizing: border-box; /* Ensure padding is included in width */
}

.input-container input:focus {
    border-color: #4CAF50;
    box-shadow: 0 0 8px rgba(76, 175, 80, 0.2);
    outline: none;
}

.input-container .prefix {
    position: absolute;
    top: 50%;
    left: 12px;
    transform: translateY(-50%);
    font-size: 16px;
    color: #333;
}

.input-container .prefix-total {
    position: absolute;
    top: 50%;
    left: 12px;
    transform: translateY(-50%);
    font-size: 16px;
    color: #333;
}

button {
    width: 100%;
    padding: 12px;
    background-color: #4CAF50;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 16px;
    cursor: pointer;
    transition: background-color 0.3s;
}

button:hover {
    background-color: #45a049;
}

#popupMessage {
    position: fixed;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    background-color: #4CAF50;
    color: white;
    padding: 20px;
    border-radius: 10px;
    display: none;
    font-size: 16px;
    text-align: center;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
    z-index: 1000; /* Ensure popup is above other elements */
}

@media (max-width: 768px) {
    form {
        padding: 15px;
    }

    h1 {
        font-size: 20px;
    }

    input, button {
        font-size: 14px;
        padding: 10px;
    }

    label {
        font-size: 12px;
    }
}

@media (max-width: 480px) {
    form {
        max-width: 90%;
        padding: 15px;
    }

    h1 {
        font-size: 18px;
    }

    input, button {
        font-size: 13px;
        padding: 10px;
    }

    label {
        font-size: 12px;
    }
}

    </style>
</head>
<body>

    <form id="orderForm">
        <h1>Form Perhitungan</h1>
<label for="orderKuota">Order Kuota:</label>
<div class="input-container">
    <span class="prefix" id="prefix1">Rp</span>
    <input type="text" id="orderKuota" name="orderKuota" oninput="formatInput(this, 'prefix1')" maxlength="15" pattern="\d*" placeholder="Masukkan angka">
</div>

<label for="saldoAllMember">Saldo All Member:</label>
<div class="input-container">
    <span class="prefix" id="prefix2">Rp</span>
    <input type="text" id="saldoAllMember" name="saldoAllMember" oninput="formatInput(this, 'prefix2')" maxlength="15" pattern="\d*" placeholder="Masukkan angka">
</div>

<label for="hutang">Hutang:</label>
<div class="input-container">
    <span class="prefix" id="prefix3">Rp</span>
    <input type="text" id="hutang" name="hutang" oninput="formatInput(this, 'prefix3')" maxlength="15" pattern="\d*" placeholder="Masukkan angka">
</div>

<label for="uangKes">Uang Kes:</label>
<div class="input-container">
    <span class="prefix" id="prefix4">Rp</span>
    <input type="text" id="uangKes" name="uangKes" oninput="formatInput(this, 'prefix4')" maxlength="15" pattern="\d*" placeholder="Masukkan angka">
</div>

        <label for="totalModal">Total Modal:</label>
        <div class="input-container">
            <span class="prefix prefix-total">Rp</span>
            <input type="text" id="totalModal" name="totalModal" disabled>
        </div>

        <button type="button" onclick="kirimKeSpreadsheet()">Kirim ke Spreadsheet</button>
    </form>

    <div id="popupMessage">Data berhasil dikirim ke Spreadsheet!</div>

    <script>
        // Fungsi untuk format angka ke format rupiah
        function formatRupiah(angka) {
            const numberString = angka.replace(/[^,\d]/g, '').toString(),
                  split = numberString.split(','),
                  sisa = split[0].length % 3,
                  rupiah = split[0].substr(0, sisa) + (split[0].substr(sisa).match(/\d{3}/g) ? '.' + split[0].substr(sisa).match(/\d{3}/g).join('.') : '');
            
            return split[1] !== undefined ? rupiah + ',' + split[1] : rupiah;
        }

        // Fungsi untuk memformat input secara otomatis dan hanya menerima angka
        function formatInput(input, prefixId) {
            let value = input.value.replace(/\./g, ''); // Hapus titik dari input
            value = value.replace(/[^0-9]/g, ''); // Hanya ambil angka
            input.value = formatRupiah(value); // Format menjadi rupiah

            // Tampilkan atau sembunyikan prefix "RP"
            const prefix = document.getElementById(prefixId);
            if (value.length > 0) {
                prefix.style.display = 'inline';
            } else {
                prefix.style.display = 'none';
            }
        }

        // Fungsi perhitungan otomatis
        function hitungTotalModal() {
            const orderKuota = parseFloat(document.getElementById('orderKuota').value.replace(/\./g, '').replace(',', '.')) || 0;
            const saldoAllMember = parseFloat(document.getElementById('saldoAllMember').value.replace(/\./g, '').replace(',', '.')) || 0;
            const hutang = parseFloat(document.getElementById('hutang').value.replace(/\./g, '').replace(',', '.')) || 0;
            const uangKes = parseFloat(document.getElementById('uangKes').value.replace(/\./g, '').replace(',', '.')) || 0;

            const totalModal = orderKuota - saldoAllMember + hutang + uangKes;

            document.getElementById('totalModal').value = formatRupiah(totalModal.toFixed(0).replace(/\B(?=(\d{3})+(?!\d))/g, '.'));
        }

        // Event listener untuk memonitor perubahan input dan menghitung total
        document.querySelectorAll('input[type="text"]').forEach(input => {
            input.addEventListener('input', hitungTotalModal);
        });

        // Fungsi untuk mengirim data ke Google Spreadsheet
        function kirimKeSpreadsheet() {
            const orderKuota = document.getElementById('orderKuota').value;
            const saldoAllMember = document.getElementById('saldoAllMember').value;
            const hutang = document.getElementById('hutang').value;
            const uangKes = document.getElementById('uangKes').value;
            const totalModal = document.getElementById('totalModal').value;

            const data = {
                orderKuota,
                saldoAllMember,
                hutang,
                uangKes,
                totalModal
            };

            fetch('https://script.google.com/macros/s/AKfycbxYO6ZT4D60oPJyDfZ3JnLG5ejtVQ0auAA_H7ThmfoVaiYnvPTcKjNy_lw31mcgqGVdsw/exec', {
                method: 'POST',
                mode: 'no-cors',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(data)
            }).then(() => {
                const popup = document.getElementById('popupMessage');
                popup.style.display = 'block';
                setTimeout(() => {
                    popup.style.display = 'none';
                }, 3000);
            }).catch(error => console.error('Error!', error.message));
        }
    </script>

</body>
</html>
