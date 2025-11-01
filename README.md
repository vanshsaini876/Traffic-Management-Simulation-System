#include <iostream>
#include <queue>
#include <string>
#include <thread>
#include <chrono>
#include <cstdlib>
using namespace std;
enum LightState { RED, GREEN, YELLOW };
class TrafficLight {
private:
    LightState state;
    int greenTime;
    int yellowTime;
    int redTime;
    int timer;
public:
    TrafficLight(int g, int y, int r)
        : greenTime(g), yellowTime(y), redTime(r), timer(0), state(RED) {}
    void update() {
        timer++;
        switch (state) {
            case GREEN:
                if (timer >= greenTime) {
                    state = YELLOW;
                    timer = 0;
                }
                break;
            case YELLOW:
                if (timer >= yellowTime) {
                    state = RED;
                    timer = 0;
                }
                break;
            case RED:
                if (timer >= redTime) {
                    state = GREEN;
                    timer = 0;
                }
                break;
        }
    }
    string getState() const {
        switch (state) {
            case GREEN: return "GREEN";
            case YELLOW: return "YELLOW";
            case RED: return "RED";
        }
        return "UNKNOWN";
    }
    bool canPass() const {
        return state == GREEN || state == YELLOW;
    }
};
class Vehicle {
private:
    string id;
public:
    Vehicle(string id) : id(id) {}
    string getId() const { return id; }
};
class Road {
private:
    queue<Vehicle> vehicles;
    TrafficLight light;
    string name;

public:
    Road(string name, int g, int y, int r)
        : name(name), light(g, y, r) {}

    void addVehicle(const Vehicle& v) {
        vehicles.push(v);
    }
    void update() {
        light.update();
        cout << "\n[" << name << "] Light: " << light.getState() << " | Vehicles: " << vehicles.size() << endl;
        if (light.canPass() && !vehicles.empty()) {
            cout << "Vehicle " << vehicles.front().getId() << " passed through " << name << "." << endl;
            vehicles.pop();
        }
    }
    void randomVehicleArrival(int chancePercent = 30) {
        if (rand() % 100 < chancePercent) {
            static int id = 1;
            addVehicle(Vehicle("V" + to_string(id++)));
            cout << "New vehicle arrived at " << name << "." << endl;
        }
    }
};
int main() {
    srand(time(0));
    Road north("North Road", 5, 2, 5);
    Road east("East Road", 5, 2, 5);
    cout << "=== Traffic Management Simulation System ===" << endl;
    cout << "Press Ctrl + C to stop simulation." << endl;
    for (int t = 0; t < 50; ++t) {
        cout << "\n--- Time Step " << t + 1 << " ---" << endl;
        north.randomVehicleArrival();
        east.randomVehicleArrival();
        north.update();
        east.update();
        this_thread::sleep_for(chrono::milliseconds(1000)); // 1-second delay per step
    }

    cout << "\nSimulation ended.\n";
    return 0;
}
