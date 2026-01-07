# AUTOMATIC-GARBAGE-COLLECTION-PROJECT
PROJECT based on garbage collection
import java.io.OutputStream;
import java.net.HttpURLConnection;
import java.net.URI;
import java.net.URL;
import java.util.Random;
public class UltrasonicBinMonitor {
    private static final String SSID = "YOUR_SSID";
    private static final String PASSWORD = "YOUR_PASS";
    private static final String SERVER_URL = "https://httpbin.org/post";
    private static double readUltrasonicDistance() {
        Random random = new Random();
        return 10 + (90 * random.nextDouble());
    }
    private static double calculateFillPercentage(double distanceCm) {
        double binHeight = 100.0; // cm
        return Math.max(0.0, Math.min(100.0, (binHeight - distanceCm) / binHeight * 100.0));
    }
    private static void sendData(double fillPct) {
        try {
            URI uri = new URI(SERVER_URL);
            URL url = uri.toURL();
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();
            conn.setRequestMethod("POST");
            conn.setRequestProperty("Content-Type", "application/json");
            conn.setDoOutput(true);
            String payload = String.format("{\"bin_id\":\"BIN123\",\"fill\":%.2f,\"battery\":88}", fillPct);
            try (OutputStream os = conn.getOutputStream()) {
                os.write(payload.getBytes("utf-8"));
            }

            int responseCode = conn.getResponseCode();
            System.out.println(" POST Response Code: " + responseCode);
            conn.disconnect();
        } catch (Exception e) {
            System.err.println(" Error sending data: " + e.getMessage());
        }
    }
    public static void main(String[] args) throws InterruptedException {
        System.out.println("Connecting to WiFi...");
        Thread.sleep(2000); // Simulate WiFi delay
        System.out.println("Connected to WiFi: " + SSID);
        while (true) {
            double distance = readUltrasonicDistance();
            double fillPct = calculateFillPercentage(distance);
            System.out.printf("Distance: %.2f cm | Fill: %.2f%%%n", distance, fillPct);
      sendData(fillPct);
            System.out.println("Sleeping for 10 minutes...\n");
            Thread.sleep(10 * 60 * 1000);
        }
   }}

