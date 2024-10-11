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
abstract class Miner {
    // TempalteMethod
    mine(path) {
        const file = this.openFile()
        if (!file) {
            throw new Error('Not found')
        }
        
        
        // Много повтор. кода
        const data = this.parseData(file)
        
        this.beforeAnalyzeData()
        const analysis = this.analyzeData(data)
        this.afterAnalyzeData()
        
        this.sendReport(analysis)
    }
    
    // Step1
    abstract openFile(path: string): void
    
    // StepN
    abstract parseData(): unknown
    
    // Hook1
    beforeAnalyzeData(): unknown {}
 
    // StepN
    analyzeData(data) {
        return analyzeData(file);
    }
    
    // HookN
    afterAnalyzeData(): unknown {}

    // StepN
    sendReport(analysis) {
        sendReport(analysis);
    }

    // TempalteMethodN
}

class CsvMiner extends Miner {
    // TempalteMethod НЕ ПЕРЕОПРЕДЕЛЯЕМ!

    openFile(path: string): void {
        return openCsvFile(path)
    }

    parseData(): unknown {
        return parseCsvData(file);
    }
}

class PdfMiner extends Miner {
    openFile(path: string): void {
        return openPdfFile(path)
    }

    parseData(): unknown {
        return parsePdfData(file);
    }

    beforeAnalyzeData(): unknown {
        console.time('analyzeData')
    }

    afterAnalyzeData(): unknown {
        console.timeEnd('analyzeData')
    }
}
```

## Вопросы

> ...
>> ...
