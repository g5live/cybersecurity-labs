# Web Security Fundamentals

## Purpose

This document records my developing understanding of web technologies and the security concepts that apply to web applications.

The objective is to understand how web applications operate before attempting to identify weaknesses within them.

## Understanding the Web Application

Before looking for vulnerabilities, I first want to understand what is being exposed and how the application behaves.

Initial questions include:

- What web service is running?
- What technologies appear to be in use?
- What pages, directories and endpoints are accessible?
- How does the application accept and process user input?
- Does the application use authentication?
- What information is exposed through responses, headers or errors?

The aim is to build an understanding of the application before forming assumptions about potential vulnerabilities.

## HTTP and HTTPS

HTTP is the protocol used to exchange requests and responses between web clients and servers.

HTTPS provides HTTP communication protected using TLS.

Understanding the request/response model is important because much of web-security testing involves examining how an application handles information sent by the client.

## Requests and Responses

A browser sends an HTTP request to a web server and receives an HTTP response.

Useful information can include:

- Request method
- Requested path
- Headers
- Parameters
- Cookies
- Response status
- Response headers
- Response content

Examining this information can help explain how the application functions and identify areas requiring further investigation.

## HTTP Methods

Common methods include:

- `GET` — retrieve a resource
- `POST` — submit data
- `PUT` — create or replace a resource
- `DELETE` — request removal of a resource

The methods supported by an application can provide information about how users interact with it.

## HTTP Status Codes

Response codes provide useful information about how a server handled a request.

Examples include:

- `200` — successful request
- `301/302` — redirection
- `403` — access forbidden
- `404` — resource not found
- `500` — server-side error

The response should be interpreted in context rather than treating any individual status code as evidence of a vulnerability.

## Web Enumeration

My current approach is to gather information progressively:

Application discovered
        ↓
Identify web service
        ↓
Inspect application
        ↓
Understand pages and functionality
        ↓
Identify inputs and endpoints
        ↓
Observe requests and responses
        ↓
Investigate interesting behaviour

As with network enumeration, the objective is to allow discovered information to guide the next step.

## Security Perspective

Web vulnerabilities often occur when an application handles user input, authentication, permissions or data incorrectly.

Areas I will develop further include:

- Authentication and session management
- Access control
- Input validation
- Injection vulnerabilities
- Directory and content discovery
- Client-side behaviour
- Server-side vulnerabilities

These areas will be expanded as I encounter and understand them through practical labs.

## Relationship to Reconnaissance

Web enumeration often begins after network reconnaissance identifies an HTTP or HTTPS service.

For example:

80/tcp   open   http
443/tcp  open   https

This does not identify a vulnerability.

Instead, it establishes another investigation path:

Open web service → inspect application → identify functionality → enumerate → form hypotheses → test appropriately

## Current Development

My current web-security experience comes primarily from practical cybersecurity training and lab environments.

This document will expand as I gain practical experience with additional web technologies, vulnerabilities and testing techniques.

Concepts will be added when I have used them and can explain what they demonstrate rather than simply building a list of vulnerability names.
