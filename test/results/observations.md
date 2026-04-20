Watch the logs in all three terminal windows as the Travel Assistant:
1. Receives a booking request from the user
2. Discovers the Flight Booking Agent through the registry
3. Sends an A2A message to the Flight Booking Agent
4. Returns the combined result

## What A2A messages were exchanged between agents (copy from logs)
The travel assistant sends a message to the flight booking agent to book a flight for the user.
```
2026-04-20 11:29:08,508,p156768,{agent.py:174},INFO,Tool called: invoke_remote_agent(agent_id='/flight-booking-agent', message='I need to book flight ID
1 for 2 passengers. Please reserve 2 seats, confirm the reservation, and pr...')
2026-04-20 11:29:08,508,p156768,{agent.py:199},INFO,Invoking agent: Flight Booking Agent
2026-04-20 11:29:08,508,p156768,{remote_agent_client.py:115},INFO,Sending message to Flight Booking Agent: I need to book flight ID 1 for 2 passengers. P
lease reserve 2 seats, confirm the reservation, and pr...
2026-04-20 11:29:08,508,p156768,{remote_agent_client.py:143},INFO,A2A REQUEST to Flight Booking Agent:
{
  "jsonrpc": "2.0",
  "method": "message/send",
  "params": {
    "message": {
      "role": "user",
      "parts": [
        {
          "kind": "text",
          "text": "I need to book flight ID 1 for 2 passengers. Please reserve 2 seats, confirm the reservation, and process the payment."
        }
      ],
      "messageId": "75e8466e40ef4fb0bbc3d75f6f0c8fce"
    }
  }
}
```

The flight booking agent then does its work and sends a response back to the travel assistant confirming the flight booking.

## How the Travel Assistant discovered the Flight Booking Agent

The travel assistant discovers the flight booking agent by calling the registry to get the available agents and what they do. The registry returns the same response every time in this demo -- the flight booking agent.

## The JSON-RPC request/response format you observed

```json
{
  "jsonrpc": "2.0",
  "method": "message/send",
  "params": {
    "message": {
      "role": "user",
      "parts": [
        {
          "kind": "text",
          "text": "I need to book flight ID 1 for 2 passengers. Please reserve 2 seats, confirm the reservation, and process the payment."
        }
      ],
      "messageId": "75e8466e40ef4fb0bbc3d75f6f0c8fce"
    }
  }
}
```

The json contains a "message/send" method to communicate and embeds the message content in the params field. There are two kinds -- Messages and Text. The receiver decides what to do with the sent message.

## What information was in the agent card and how it was used

The agent card contains all the information necessary to interact with the agent. It contains the name, the endpoint url, the preferred method of communication (JSONRPC), and all the skills that the agent can perform (and their descriptions). It is used by the travel assistant to figure out what the capabilities of the flight booking agent are and how to communicate with it.
```json
{
  "capabilities": { "streaming": true },
  "defaultInputModes": ["text"],
  "defaultOutputModes": ["text"],
  "description": "Flight booking and reservation management agent",
  "name": "Flight Booking Agent",
  "preferredTransport": "JSONRPC",
  "protocolVersion": "0.3.0",
  "skills": [
    {
      "description": "Check seat availability for a specific flight.",
      "id": "check_availability",
      "name": "check_availability",
      "tags": []
    },
    {
      "description": "Reserve seats on a flight for passengers.",
      "id": "reserve_flight",
      "name": "reserve_flight",
      "tags": []
    },
    {
      "description": "Confirm and finalize a flight booking.",
      "id": "confirm_booking",
      "name": "confirm_booking",
      "tags": []
    },
    {
      "description": "Process payment for a booking (simulated).",
      "id": "process_payment",
      "name": "process_payment",
      "tags": []
    },
    {
      "description": "Update, view, or cancel existing reservations.",
      "id": "manage_reservation",
      "name": "manage_reservation",
      "tags": []
    }
  ],
  "url": "http://127.0.0.1:10002/",
  "version": "0.0.1"
}
```

## Your observations about the benefits and limitations of this approach

This system seems beneficial because it provides a uniform way for agents to communicate with each other. The system of cards also makes it really easy for agents to be added or deleted, as well as makes it easy for agents to know about each other's capabilities. Th elimitations I can think of are that the registry must be active and working, and the capabilities of each agent must be accurately interpreted by the other agents. 
