# BetterReset
<img width="300" alt="スクリーンショット 2023-02-11 18 33 27" src="https://user-images.githubusercontent.com/47273077/218251731-847c1990-863c-4f8c-b952-33b7c6ad14e4.gif">

```swift

struct ContentView: View {
    @State private var wakeUp = defaultWakeTime
    @State private var sleepAmount = 0.0
    @State private var coffeeAmount = 1
    
    @State private var alertTitle = ""
    @State private var alertMessage = ""
    @State private var showingAlert = false
    
    static var defaultWakeTime: Date {
        var components = DateComponents()
        components.hour = 7
        components.minute = 0
        return Calendar.current.date(from: components) ?? Date.now
    }

    var body: some View {
        NavigationView {
            Form {
                VStack(alignment: .leading, spacing: 0) {
                    Text("When do you want to wake up?")
                        .font(.headline)
                    
                    DatePicker("Please enter a time", selection: $wakeUp, displayedComponents:
                            .hourAndMinute)
                    .labelsHidden()
                }

                VStack(alignment: .leading, spacing: 0) {
                    Text("Desired amount of sleep")
                        .font(.headline)
                    
                    Stepper("\(sleepAmount.formatted()) hours", value: $sleepAmount, in: 4...12, step: 0.25)
                }

                VStack(alignment: .leading, spacing: 0) {
                    Text("Daily coffee intake")
                        .font(.headline)
                    
                    Stepper(coffeeAmount == 1 ? "1 cup" : "\(coffeeAmount) coups", value: $coffeeAmount, in: 1...20)
                }
            }
            .navigationTitle("BetterRest")
            .toolbar {
                Button("Calculate", action: calucateBedTime)
            }
            .alert(alertTitle, isPresented: $showingAlert) {
                Button("OK") {}
            } message: {
                Text(alertMessage)
            }
        }
    }
    
    func calucateBedTime() {
        do {
            let config = MLModelConfiguration()
            let model = try SleepCalculator(configuration: config)
            
            print("****wakeUp***\(wakeUp)")
            print("****sleepAmount***\(sleepAmount)")
            print("****coffeeAmount***\(coffeeAmount)")
            let components = Calendar.current.dateComponents([.hour, .minute], from: wakeUp)
            let hour = (components.hour ?? 0) * 60 * 60
            let minute = (components.minute ?? 0) * 60
            
            print("*****hour****\(hour)")
            print("*****minute****\(minute)")
            
            let prediction = try model.prediction(wake: Double(hour * minute), estimatedSleep: sleepAmount, coffee: Double(coffeeAmount))
            
            let sleepTime = wakeUp - prediction.actualSleep
            alertTitle = "Your ideal bedTime is.."
            alertMessage = sleepTime.formatted(date: .omitted, time: .shortened)
        } catch {
            alertTitle = "Error"
            alertMessage = "Sorry"
        }
        
        showingAlert = true
        
    }

}

```

CSV
https://github.com/twostraws/HackingWithSwift/blob/main/SwiftUI/project4-files/BetterRest.csv
<img width="928" alt="スクリーンショット 2023-02-11 18 32 34" src="https://user-images.githubusercontent.com/47273077/218251104-ef32d4e8-6e05-4aac-95a7-78d9fe9a5842.png">

