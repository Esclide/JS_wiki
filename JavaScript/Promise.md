Promise

https://javascript.info/promisify

Promisification - преобразование функции, принимающей коллбэк, в функцию, возвращающую промис

  function promisify(f) {
    return (...args) => new Promise((resolve, reject) => {
        function callback(err, result) { // our custom callback for f (**)
          if (err) {
            reject(err);
          } else {
            resolve(result);
          }
        }

        args.push(callback); // append our custom callback to the end of f arguments

        f(...args); // call the original function
      });
  }

Т.е. создаем новую функцию-колбэк и передаем ее в изначальную функцию, затем ее вызываем

Таймаут для промисов

  const resolveWithTimeout = (prom, time) =>
  Promise.race([prom, (new Promise((_r, rej) => setTimeout(() => rej('ERROR'), time)))]);

  function timedPromises(promise, timeout) {
    if (timeout == null) {
      return promise;
    }

    return resolveWithTimeout(new Promise((resolve) => setTimeout(resolve, timeout, promise)), timeout)
  }

