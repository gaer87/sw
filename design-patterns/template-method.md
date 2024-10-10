## До

```ts
class CsvMiner {
    mine(path) {
        const file = openCsvFile(path)
        if (!file) {
            throw new Error('Not found')
        }
        
        const data = parseCsvData(file)
        const analysis = analyzeData(data)
        sendReport(analysis)
    }
}

class PdfMiner {
    mine(path) {
        const file = openPdfFile(path)
        if (!file) {
            throw new Error('Not found')
        }
        
        const data = parsePdfData(file)
        const analysis = analyzeData(data)
        sendReport(analysis)
    }
}
```

## После

```ts
```

## Вопросы

> ...
>> ...
