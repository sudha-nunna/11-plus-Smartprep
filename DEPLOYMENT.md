# Deployment Guide

This guide explains how to deploy the SmartPrep application with the frontend on Vercel and backend on Render.

## Prerequisites

- GitHub account
- Vercel account (for frontend)
- Render account (for backend)
- MongoDB database (MongoDB Atlas recommended)

## Backend Deployment (Render)

### 1. Push Backend to GitHub

Make sure your backend code is in a GitHub repository.

### 2. Deploy on Render

1. Go to [Render Dashboard](https://dashboard.render.com)
2. Click "New +" → "Web Service"
3. Connect your GitHub repository
4. Configure the service:
   - **Name**: `smartprep-backend`
   - **Environment**: `Node`
   - **Build Command**: `npm install`
   - **Start Command**: `npm start`
   - **Root Directory**: `BACKEND` (already configured in render.yaml)
   
   **Note**: If using render.yaml, Render will auto-detect the root directory. Otherwise, manually set it to `BACKEND`.

### 3. Environment Variables on Render

Add these environment variables in Render dashboard:

```
NODE_ENV=production
PORT=10000
MONGO_URI=your-mongodb-connection-string
JWT_SECRET=your-secret-key-here
CLIENT_URL=https://your-frontend-url.vercel.app
```

**Note**: Replace `your-mongodb-connection-string` with your actual MongoDB Atlas connection string, and `your-frontend-url.vercel.app` with your Vercel frontend URL.

### 4. Get Backend URL

After deployment, Render will provide a URL like: `https://smartprep-backend.onrender.com`

## Frontend Deployment (Vercel)

### 1. Push Frontend to GitHub

Make sure your frontend code is in a GitHub repository.

### 2. Deploy on Vercel

1. Go to [Vercel Dashboard](https://vercel.com/dashboard)
2. Click "Add New..." → "Project"
3. Import your GitHub repository
4. Configure the project:
   - **Framework Preset**: Next.js
   - **Root Directory**: `FRONTEND` (already configured in vercel.json)
   - **Build Command**: `npm run build` (should auto-detect)
   - **Output Directory**: Leave EMPTY or set to `.next` (DO NOT use "Next.js default" as text - it will be treated as a literal path)
   
   **Important**: In the Output Directory field, either:
   - Leave it completely empty (recommended - Vercel will auto-detect)
   - OR type `.next` explicitly
   - DO NOT type "Next.js default" - this causes deployment errors
   
   **Note**: Vercel should auto-detect the root directory from vercel.json. If not, manually set it to `FRONTEND`.

### 3. Environment Variables on Vercel

Add this environment variable in Vercel project settings:

```
NEXT_PUBLIC_API_URL=https://your-backend-url.onrender.com
```

**Note**: Replace `your-backend-url.onrender.com` with your actual Render backend URL.

### 4. Update Backend CORS

After getting your Vercel frontend URL, update the `CLIENT_URL` environment variable in Render to match your Vercel URL.

## Important Notes

1. **CORS Configuration**: The backend CORS is configured to accept requests from:
   - The URL specified in `CLIENT_URL` environment variable
   - `http://localhost:3000` (for local development)

2. **API URLs**: The frontend uses `NEXT_PUBLIC_API_URL` environment variable. Make sure this is set correctly in Vercel.

3. **MongoDB**: Use MongoDB Atlas for production database. Update `MONGO_URI` in Render with your Atlas connection string.

4. **JWT Secret**: Use a strong, random secret for `JWT_SECRET` in production.

## Local Development

For local development, create `.env.local` files:

**Frontend** (`Smart-prep/FRONTEND/.env.local`):
```
NEXT_PUBLIC_API_URL=http://localhost:5000
```

**Backend** (`Smart-prep/BACKEND/.env`):
```
PORT=5000
NODE_ENV=development
MONGO_URI=mongodb://localhost:27017/smartprep
JWT_SECRET=your-secret-key-here
CLIENT_URL=http://localhost:3000
```

## Troubleshooting

- **CORS Errors**: Make sure `CLIENT_URL` in backend matches your frontend URL exactly
- **API Connection Issues**: Verify `NEXT_PUBLIC_API_URL` is set correctly in Vercel
- **Database Connection**: Check MongoDB Atlas network access settings allow Render IPs
- **Build Failures**: Check build logs in Vercel/Render dashboards for specific errors

