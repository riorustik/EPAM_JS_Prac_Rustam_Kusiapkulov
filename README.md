# Аудио-вещатель

Учебный проект, реализован в 2021 году на языке **`JavaScript`**. 

WEB-приложение, с использованием сервера **`Heroku`** которое позволяет записать свой голос и транслироваать его всем, у кого открыто приложение. Пользователи могут прослушать последние 5 записей.

###### Демонстрация
<dl>
  <dd>
    <dl>
      <dd>
        <h3><a href="https://riorustik.github.io/EPAM_JS_Prac_Rustam_Kusiapkulov/">GitHub Pages</a> :octocat:</h3>
      </dd>
    </dl>
  </dd>
</dl> 

### Проект написан с использованием сервера `Heroku`.

Функция для записи голоса
```
function handleSuccess(){ 
    const socket = io.connect('https://voicy-speaker.herokuapp.com/');

    navigator.mediaDevices.getUserMedia({ audio: true, video: false }).then(function(stream) {
        const options = {mimeType: 'audio/webm'};
        const mediaRecorder = new MediaRecorder(stream, options);
        mediaRec = mediaRecorder;
        const audioChunks = [];

        mediaRecorder.start();

        mediaRecorder.addEventListener("dataavailable", event => {
            audioChunks.push(event.data);
        });

        mediaRecorder.addEventListener("stop", () => {
            socket.emit('audioMessage', audioChunks);
        });
    });
};
```

Функция для прослушивания последних 5 записей
```
export async function voices() {

    audioBlock.innerHTML = "";
    const response = await fetch('https://voicy-speaker.herokuapp.com/voices');
    const data = await response.json();
    
    for (let i = data.length - 5; i < data.length; i++) {
        const audio = document.createElement('audio');
        const li = document.createElement('li');
        const audioBlob = new Blob([new Uint8Array(data[i].audioBlob[0].data).buffer]);
        const audioUrl = URL.createObjectURL(audioBlob);
     
        audio.controls = true;
        audio.src = audioUrl;

        li.appendChild(audio);
        audioBlock.appendChild(li);
    }
}
```

Функция для трансляции записанного голоса в автоматическом режиме
```
function stream(){

    const socket = io.connect('https://voicy-speaker.herokuapp.com/');

    socket.on('audioMessage', function (audioChunks) {
        const audioBlob = new Blob(audioChunks);
        const audioUrl = URL.createObjectURL(audioBlob);
        const audio = new Audio(audioUrl);
        audio.play();
    });
}
```
