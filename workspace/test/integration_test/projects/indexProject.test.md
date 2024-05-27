import { server } from '../../../src/app.js'
import supertest from 'supertest'
const request = supertest(server)
import { randomUUID } from 'crypto'

import {
  adminPassword,
  mockPrjName2,
} from '../data/project.js'
import projectsService from '../requiredTest/projectService.js'
import messages from '../../../src/common/messages.js'
import usersService from '../requiredTest/userService.js'
import { mockPassword } from '../data/user.js'

export const projectIndex = () => {
  describe('INDEX PROJECT ', () => {
    let userId
    let token
    let superAdminId
    let adminId
    let userToken
    let adminToken
    let projectId

    beforeAll(async () => {
      try {
        const superAdminInfo = {
          username: randomUUID(),
          password: adminPassword,
          role: 'superAdmin',
        }

        superAdminId = await usersService.create(superAdminInfo)

        delete superAdminInfo.role

        token = await usersService.login(superAdminInfo)

        const userInfo = {
          username: randomUUID(),
          password: mockPassword,
          role: 'user',
        }

        userId = await usersService.create(userInfo)

        delete userInfo.role

        userToken = await usersService.login(userInfo)

        const adminInfo = {
          username: randomUUID(),
          password: mockPassword,
          role: 'admin',
        }

        adminId = await usersService.create(adminInfo)

        delete adminInfo.role

        adminToken = await usersService.login(adminInfo)

        const project = {
          name: mockPrjName2,
        }

        projectId = await projectsService.create(project)

      } catch (error) {
        console.log(error)
      }
    })

    afterAll(async () => {
      await projectsService.delete(projectId)
      await usersService.delete(superAdminId)
      await usersService.delete(userId)
      await usersService.delete(adminId)
    })

    it('projects have been found', (done) => {
      try {
        request
          .get('/api/projects/?limit=3&offs=1')
          .set('Authorization', `Bearer ${token}`)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('admins have access to get projects', (done) => {
      try {
        request
          .get('/api/projects/?limit=3&offs=1')
          .set('Authorization', `Bearer ${adminToken}`)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('users don\'t have access to this api', (done) => {
      try {
        request
          .get('/api/projects/?limit=3&offs=1')
          .set('Authorization', `Bearer ${userToken}`)
          .expect(403)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.ACCESS_DENIED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('field incorrect in params', (done) => {
      try {
        request
          .get('/api/projects/?limit=3&offset=1')
          .set('Authorization', `Bearer ${token}`)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.FIELD_INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('offs has been written incorrectly. it should be a number', (done) => {
      try {
        request
          .get('/api/projects/?limit=3&offs=one')
          .set('Authorization', `Bearer ${token}`)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PAGE} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('limit has been written incorrectly. it should be a number', (done) => {
      try {
        request
          .get('/api/projects/?limit=three&offs=1')
          .set('Authorization', `Bearer ${token}`)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.LIMIT} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('endTimeStamp should be a valid date', (done) => {
      try {
        request
          .get('/api/projects/?endTimeStamp=asdfsdfsdf')
          .set('Authorization', `Bearer ${token}`)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.END_TIMESTAMP} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('startTimeStamp should be a valid date', (done) => {
      try {
        request
          .get('/api/projects/?startTimeStamp=asdfsdfsdf')
          .set('Authorization', `Bearer ${token}`)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.START_TIMESTAMP} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('token is expired', (done) => {
      try {
        const expiredToken =
          'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjNhZjZhZWIyLTg3M2EtNDNiNS04ZDM0LTE0MzczZGE2ZTFiMSIsImlhdCI6MTY2NjUwNjk3MH0.o1k__1jtkL3C2Kte'

        request
          .get('/api/projects')
          .set('Authorization', `Bearer ${expiredToken}`)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.TOKEN_EXPIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('login needed', (done) => {
      try {
        request
          .get('/api/projects')
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.LOGIN_NEEDED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })
  })
}
