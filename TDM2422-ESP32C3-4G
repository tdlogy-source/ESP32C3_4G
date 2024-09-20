#include <HardwareSerial.h>
#define ARDUINO_USB_CDC_ON_BOOT   = 1
#define ARDUINO_USB_MODE         = 0
#define simSerial               Serial0 
#define MCU_SIM_BAUDRATE        115200
#define MCU_SIM_TX_PIN          21 
#define MCU_SIM_RX_PIN          20 
#define MCU_SIM_EN_PIN          9  

// Số điện thoại để kiểm tra
#define PHONE_NUMBER            "Nhập số điện thoại"

void sim_at_wait() {
    delay(100);
    while (simSerial.available()) {
        Serial.write(simSerial.read());  
    }
}

bool sim_at_cmd(String cmd) {
    simSerial.println(cmd);
    sim_at_wait();
    return true;
}

bool sim_at_send(char c) {
    simSerial.write(c);
    return true;
}

void sent_sms() {
    sim_at_cmd("AT+CMGF=1");
    String temp = "AT+CMGS=\"";
    temp += PHONE_NUMBER;
    temp += "\"";
    sim_at_cmd(temp);
    sim_at_cmd("helloFromLktd");

    // Ký tự kết thúc tin nhắn
    sim_at_send(0x1A);
}

void call() {
    String temp = "ATD";
    temp += PHONE_NUMBER;
    temp += ";";
    sim_at_cmd(temp); 

    delay(20000);

    // Kết thúc cuộc gọi
    sim_at_cmd("ATH"); 
}

void setup() {
    // Kích hoạt nguồn cho module SIM
    pinMode(MCU_SIM_EN_PIN, OUTPUT); 
    digitalWrite(MCU_SIM_EN_PIN, LOW);

    delay(20);
    Serial.begin(115200);  // Serial cho in log
    Serial.println("\n\n\n\n-----------------------\nHệ thống đã khởi động!!!!");

    // Trì hoãn 8s để module SIM khởi động
    delay(8000);
    simSerial.begin(MCU_SIM_BAUDRATE, SERIAL_8N1, MCU_SIM_RX_PIN, MCU_SIM_TX_PIN); // Bắt đầu UART cho SIM

    // Kiểm tra lệnh AT
    sim_at_cmd("AT");

    // Thông tin sản phẩm
    sim_at_cmd("ATI");

    // Kiểm tra SIM
    sim_at_cmd("AT+CPIN?");

    // Kiểm tra chất lượng tín hiệu
    sim_at_cmd("AT+CSQ");
    sim_at_cmd("AT+CCID");
    sim_at_cmd("AT+CFSN");
    sim_at_cmd("AT+CGSN");
    sim_at_cmd("AT+GMM=?");
    sim_at_cmd("AT+CIMI");

    sent_sms();
    delay(1000);   
    call();
}

void loop() {     
    if (Serial.available()) {
        char c = Serial.read();
        simSerial.write(c);
    }
    sim_at_wait();
}
