Demo brain master
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Countdown Timer</title>
<style>
    table {
        border-collapse: collapse;
        width: 50%;
    }
    th, td {
        border: 1px solid #dddddd;
        text-align: left;
        padding: 8px;
    }
    th {
        background-color: #f2f2f2;
    }
	tr:hover {
  background-color:  #f0f0f0;
}
	
</style>
</head>
<body>

<input type="file" id="fileInput" accept=".txt">
<button onclick="loadData()">Load Data</button>

<table id="countdownTable">
    <thead>
        <tr>
            <th>Land</th>
            <th>End</th>
            <th>Time</th>
            <th>Custom</th>
            <th>Next</th>
        </tr>
    </thead>
    <tbody>
    </tbody>
</table>

<button onclick="exportData()">Export</button>

<script>
    var data = [];

    function startCountdown(row) {
        

        var customTime = parseInt(document.getElementsByClassName('customInput')[row - 1].value);
        if (isNaN(customTime) || customTime <= 0) {
            alert("Please enter a valid number greater than zero.");
            return;
        }

        clearInterval(data[row - 1].countdownInterval); // Dừng đếm ngược hiện tại nếu có

        var timeLeft = customTime;
        var countdownElement = document.getElementById('countdown' + row);
        var endElement = document.querySelectorAll('#countdownTable tbody tr')[row - 1].cells[1].querySelector('span');

        // Tính thời gian hoàn thành đếm ngược
        var endTime = new Date(Date.now() + customTime * 1000); // Thêm thời gian đếm ngược vào thời gian hiện tại
        endElement.textContent = endTime.toLocaleString(); // Cập nhật giá trị của cột "End" dưới dạng văn bản

        /*data[row - 1].countdownInterval = setInterval(function() {
            if (timeLeft <= 0) {
                clearInterval(data[row - 1].countdownInterval);
                countdownElement.textContent = "Time's up!";
                countdownElement.parentNode.style.backgroundColor = "lightblue"; // Nền màu xanh dương
            } else {
                countdownElement.textContent = formatTime(timeLeft);
                if (timeLeft <= 60) {
                    countdownElement.parentNode.style.backgroundColor = "lightgreen"; // Nền màu xanh lá cây
                } else if (timeLeft <= 180) {
                    countdownElement.parentNode.style.backgroundColor = "yellow"; // Nền màu vàng
                } else if (timeLeft <= 300) {
                    countdownElement.parentNode.style.backgroundColor = "orange"; // Nền màu cam
                }
                timeLeft--;
            }
        }, 1000);*/

      
    }


	
    function formatTime(seconds) {
        var hours = Math.floor(seconds / 3600);
        var minutes = Math.floor((seconds % 3600) / 60);
        var remainingSeconds = seconds % 60;
        return hours.toString().padStart(2, '0') + ":" + minutes.toString().padStart(2, '0') + ":" + remainingSeconds.toString().padStart(2, '0');
    }

    function loadData() {
        var fileInput = document.getElementById('fileInput');
        var file = fileInput.files[0];

        if (!file) {
            alert("Please select a file.");
            return;
        }

        var reader = new FileReader();
        reader.onload = function(event) {
            var text = event.target.result;
            var rows = text.trim().split('\n');
            var tableBody = document.querySelector('#countdownTable tbody');
            tableBody.innerHTML = ''; // Xóa dữ liệu cũ

            data = []; // Xóa dữ liệu cũ khi tải tệp mới

            rows.forEach((row, index) => {
                var [land, endDate, custom] = row.split(',');
                var rowData = { land: land, endDate: endDate, custom: custom };
                data.push(rowData); // Lưu dữ liệu vào mảng data
                var tableRow = document.createElement('tr');
                tableRow.innerHTML = `
                    <td>${land}</td>
                    <td><span>${endDate}</span></td>
                    <td id="countdown${index + 1}">
						<input type="radio" name="time${index}">
                        <input type="radio" name="time${index}">
                        <input type="radio" name="time${index}">
					</td>
                    <td><input type="number" value="${custom}" class="customInput"></td>
                    <td><button onclick="startCountdown(${index + 1})">Next</button></td>
                `;
                tableBody.appendChild(tableRow);
            });
        };
        reader.readAsText(file);
    }

    function exportData() {
    var filename = 'countdown_data.txt';
    var dataToExport = data.map(row => `${row.land},${document.querySelector('#countdownTable tbody tr:nth-child(' + (data.indexOf(row) + 1) + ') td:nth-child(2) span').textContent},${document.querySelector('#countdownTable tbody tr:nth-child(' + (data.indexOf(row) + 1) + ') td:nth-child(4) input').value}`).join('\n'); // Lấy nội dung của thẻ span trong cột "End" và giá trị của ô input trong cột "Custom"
    var blob = new Blob([dataToExport], { type: 'text/plain' });
    
    if (window.navigator.msSaveOrOpenBlob) {
        window.navigator.msSaveBlob(blob, filename);
    } else {
        var a = document.createElement('a');
        var url = URL.createObjectURL(blob);
        a.href = url;
        a.download = filename;
        document.body.appendChild(a);
        a.click();
        setTimeout(function() {
            document.body.removeChild(a);
            window.URL.revokeObjectURL(url);
        }, 0);
    }
}



</script>

</body>
</html>
