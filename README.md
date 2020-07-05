
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>My Chat App</title>
</head>
<body>
<h1>Welcome to chat room {{ room }}</h1>

<div id="messages"></div>

<form id="message_input_form">
    <input type="text" id="message_input" placeholder="Enter your message here">
    <button type="submit">Send</button>
</form>
</body>
<script src="https://cdnjs.cloudflare.com/ajax/libs/socket.io/2.3.0/socket.io.js"></script>
<script>
    const socket = io.connect("http://127.0.0.1:5000");

    socket.on('connect', function () {
        socket.emit('join_room', {
            username: "{{ username }}",
            room: "{{ room }}"
        });

        let message_input = document.getElementById('message_input');

        document.getElementById('message_input_form').onsubmit = function (e) {
            e.preventDefault();
            let message = message_input.value.trim();
            if (message.length) {
                socket.emit('send_message', {
                    username: "{{ username }}",
                    room: "{{ room }}",
                    message: message
                })
            }
            message_input.value = '';
            message_input.focus();
        }
    });

    window.onbeforeunload = function () {
        socket.emit('leave_room', {
            username: "{{ username }}",
            room: "{{ room }}"
        })
    };

    socket.on('receive_message', function (data) {
        console.log(data);
        const newNode = document.createElement('div');
        newNode.innerHTML = `<b>${data.username}:&nbsp;</b> ${data.message}`;
        document.getElementById('messages').appendChild(newNode);
    });

    socket.on('join_room_announcement', function (data) {
        console.log(data);
        if (data.username !== "{{ username }}") {
            const newNode = document.createElement('div');
            newNode.innerHTML = `<b>${data.username}</b> has joined the room`;
            document.getElementById('messages').appendChild(newNode);
        }
    });

    socket.on('leave_room_announcement', function (data) {
        console.log(data);
        const newNode = document.createElement('div');
        newNode.innerHTML = `<b>${data.username}</b> has left the room`;
        document.getElementById('messages').appendChild(newNode);
    });
</script>
</html>
